# Dice-Engine v1.1.0 Enhancements — Implementation Plan

> **Spec**: [spec.md](./spec.md)  
> **Status**: READY FOR REVIEW  
> **Target**: March 2026

---

## Overview

This plan implements 5 user stories for v1.1.0 Enhancements:
1. **US-1**: Exploding Dice
2. **US-2**: Mortal Wound Cascades
3. **US-3**: Advanced Re-Roll Mechanics
4. **US-4**: Modifier Stacking
5. **US-5**: Combat Sequence Builder

---

## Proposed Changes

### Layer 1: Data Models

---

#### [MODIFY] [models.py](file:///c:/Users/bfoxt/.gemini/antigravity/playground/twilight-halo/Dice-Engine/src/dice_engine/models.py)

**Add new enums and dataclasses:**

```python
from enum import Enum

class RerollPolicy(Enum):
    """Re-roll policy for combat phases."""
    NONE = "none"
    ONES = "ones"      # Re-roll 1s only
    FAILED = "failed"  # Re-roll all failed
    ALL = "all"        # Re-roll all dice

@dataclass
class Modifiers:
    """Combat roll modifiers (+1/-1 to hit/wound/save)."""
    hit: int = 0
    wound: int = 0
    save: int = 0
    
    def __post_init__(self):
        for name, val in [("hit", self.hit), ("wound", self.wound), ("save", self.save)]:
            if not isinstance(val, int):
                raise TypeError(f"{name} must be int, got {type(val)}")

@dataclass
class ExplodingRoll:
    """A dice roll that may explode into additional rolls."""
    base_roll: DiceRoll
    explosions: list[DiceRoll] = field(default_factory=list)
    
    @property
    def total(self) -> int:
        return self.base_roll.value + sum(r.value for r in self.explosions)
    
    @property
    def count(self) -> int:
        return 1 + len(self.explosions)
```

**Update `CombatResult`:**

```python
@dataclass
class CombatResult:
    # ... existing fields ...
    
    # NEW: Mortal wound tracking
    mortal_wounds: int = 0
    
    # NEW: Re-roll tracking
    hit_rerolls: int = 0
    wound_rerolls: int = 0
    save_rerolls: int = 0
```

---

### Layer 2: Engine Core

---

#### [MODIFY] [engine.py](file:///c:/Users/bfoxt/.gemini/antigravity/playground/twilight-halo/Dice-Engine/src/dice_engine/engine.py)

**Add exploding dice method:**

```python
def roll_exploding(
    self,
    sides: int = 6,
    explode_on: int = 6,
    max_explosions: int = 10
) -> ExplodingRoll:
    """
    Roll a die that may explode on high values.
    
    Args:
        sides: Number of sides.
        explode_on: Threshold for explosion (>=).
        max_explosions: Maximum explosion depth.
    
    Returns:
        ExplodingRoll with base + explosions.
    """
```

**Add modifier application helper:**

```python
def _apply_modifier(self, roll: int, modifier: int) -> int:
    """Apply modifier with clamping to 1-6."""
    return max(1, min(6, roll + modifier))

def _check_success(
    self,
    roll: DiceRoll,
    target: int,
    modifier: int = 0,
    auto_success_on_6: bool = False
) -> bool:
    """
    Check if a roll succeeds against target.
    
    - Unmodified 1 always fails
    - Unmodified 6 always succeeds (if auto_success_on_6=True)
    - Otherwise compare modified roll to target
    """
```

---

### Layer 3: Combat Builder

---

#### [NEW] [builder.py](file:///c:/Users/bfoxt/.gemini/antigravity/playground/twilight-halo/Dice-Engine/src/dice_engine/builder.py)

```python
"""
CombatSequenceBuilder: Fluent API for complex combat sequences.
"""

from __future__ import annotations
from typing import Self
from dice_engine.engine import DiceEngine
from dice_engine.models import (
    CombatResult, RerollPolicy, Modifiers
)


class CombatSequenceBuilder:
    """
    Fluent builder for Warhammer 40K combat sequences.
    
    Example:
        result = (
            CombatSequenceBuilder(engine)
            .attacks(10)
            .hitting(3)
            .wounding(4)
            .saving(5)
            .damage(2)
            .with_rerolls(hit=RerollPolicy.ONES)
            .with_modifiers(Modifiers(hit=1))
            .with_exploding_hits(on=6, extra=1)
            .with_mortal_wounds(on=6, count=1)
            .execute()
        )
    """
    
    def __init__(self, engine: DiceEngine | None = None):
        self._engine = engine or DiceEngine()
        self._attacks: int | None = None
        self._hit_on: int | None = None
        self._wound_on: int | None = None
        self._save_on: int | None = None
        self._damage_per: int | None = None
        self._hit_reroll = RerollPolicy.NONE
        self._wound_reroll = RerollPolicy.NONE
        self._save_reroll = RerollPolicy.NONE
        self._modifiers = Modifiers()
        self._exploding_on: int | None = None
        self._exploding_extra: int = 1
        self._mortal_on: int | None = None
        self._mortal_count: int = 1
    
    def attacks(self, count: int) -> Self: ...
    def hitting(self, target: int) -> Self: ...
    def wounding(self, target: int) -> Self: ...
    def saving(self, target: int) -> Self: ...
    def damage(self, per_wound: int) -> Self: ...
    def with_rerolls(
        self,
        hit: RerollPolicy = RerollPolicy.NONE,
        wound: RerollPolicy = RerollPolicy.NONE,
        save: RerollPolicy = RerollPolicy.NONE
    ) -> Self: ...
    def with_modifiers(self, mods: Modifiers) -> Self: ...
    def with_exploding_hits(self, on: int = 6, extra: int = 1) -> Self: ...
    def with_mortal_wounds(self, on: int = 6, count: int = 1) -> Self: ...
    def validate(self) -> list[str]: ...
    def execute(self) -> CombatResult: ...
```

---

### Layer 4: Package Exports

---

#### [MODIFY] [__init__.py](file:///c:/Users/bfoxt/.gemini/antigravity/playground/twilight-halo/Dice-Engine/src/dice_engine/__init__.py)

```python
from dice_engine.engine import DiceEngine
from dice_engine.models import (
    DiceRoll,
    CombatResult,
    BatchRollResult,
    # NEW exports
    RerollPolicy,
    Modifiers,
    ExplodingRoll,
)
from dice_engine.builder import CombatSequenceBuilder

__all__ = [
    "DiceEngine",
    "DiceRoll",
    "CombatResult",
    "BatchRollResult",
    "RerollPolicy",
    "Modifiers",
    "ExplodingRoll",
    "CombatSequenceBuilder",
]
```

---

### Layer 5: Test Suite

---

#### [MODIFY] [test_engine.py](file:///c:/Users/bfoxt/.gemini/antigravity/playground/twilight-halo/Dice-Engine/tests/test_engine.py)

Add test classes:

```python
class TestExplodingDice:
    """Tests for exploding dice mechanics."""
    
    def test_no_explosion(self): ...
    def test_single_explosion(self): ...
    def test_recursive_explosion(self): ...
    def test_max_depth_cap(self): ...

class TestModifiers:
    """Tests for modifier stacking."""
    
    def test_positive_modifier(self): ...
    def test_negative_modifier(self): ...
    def test_clamp_overflow(self): ...
    def test_clamp_underflow(self): ...
    def test_unmodified_1_always_fails(self): ...
    def test_unmodified_6_auto_success(self): ...

class TestRerollPolicies:
    """Tests for re-roll mechanics."""
    
    def test_reroll_ones(self): ...
    def test_reroll_failed(self): ...
    def test_no_reroll_reroll(self): ...
```

---

#### [NEW] [test_builder.py](file:///c:/Users/bfoxt/.gemini/antigravity/playground/twilight-halo/Dice-Engine/tests/test_builder.py)

```python
"""Tests for CombatSequenceBuilder."""

class TestCombatSequenceBuilder:
    """Tests for the fluent combat builder."""
    
    def test_basic_sequence(self): ...
    def test_with_rerolls(self): ...
    def test_with_modifiers(self): ...
    def test_with_exploding_hits(self): ...
    def test_with_mortal_wounds(self): ...
    def test_validation_missing_attacks(self): ...
    def test_validation_negative_damage(self): ...
    def test_combined_all_features(self): ...
```

---

## Verification Plan

### Automated Tests

**Command to run all tests:**
```powershell
cd c:\Users\bfoxt\.gemini\antigravity\playground\twilight-halo\Dice-Engine
uv run pytest tests/ -v --tb=short
```

**Expected result:** All existing tests pass + new tests pass (target: 30+ total tests).

**Coverage check:**
```powershell
uv run pytest tests/ --cov=dice_engine --cov-report=term-missing
```

**Expected result:** 90%+ coverage on new code.

---

### Manual Verification

1. **REPL Smoke Test**: After implementation, run:
   ```python
   from dice_engine import DiceEngine, CombatSequenceBuilder, RerollPolicy, Modifiers
   
   engine = DiceEngine(seed=b"test")
   result = (
       CombatSequenceBuilder(engine)
       .attacks(10)
       .hitting(3)
       .wounding(4)
       .saving(5)
       .damage(2)
       .with_rerolls(hit=RerollPolicy.ONES)
       .with_modifiers(Modifiers(hit=1))
       .execute()
   )
   print(result)
   ```
   **Expected**: CombatResult prints with attack sequence (e.g., `10A → 8H → 5W → 6D`).

2. **Backward Compatibility Check**: Existing `combat_roll()` usage must still work:
   ```python
   result = engine.combat_roll(attacks=5, hit_on=3, wound_on=4, save=5, damage=1)
   print(result.damage_dealt)
   ```
   **Expected**: Returns valid integer damage.

---

## Implementation Order

1. **models.py** — Add enums + dataclasses (no dependencies)
2. **engine.py** — Add `roll_exploding()` and modifier helpers
3. **builder.py** — Create new file with `CombatSequenceBuilder`
4. **__init__.py** — Update exports
5. **test_engine.py** — Add new test classes
6. **test_builder.py** — Create builder tests
7. **Final verification** — Run full test suite

---

*Spec-Bot • `/speckit.plan` complete*
