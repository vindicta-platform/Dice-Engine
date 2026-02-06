# Dice-Engine v1.1.0 Enhancements Specification

> **Feature Bundle**: v1.1.0 Enhancements  
> **Status**: CLARIFIED (Cycle 3/3 Complete)  
> **Author**: Spec-Bot  
> **Date**: 2026-02-05  
> **Target**: March 2026

---

## 1. Overview

This specification defines the v1.1.0 enhancement bundle for Dice-Engine, introducing advanced Warhammer 40K 10th Edition dice mechanics including exploding dice, mortal wound cascades, comprehensive re-roll mechanics, modifier stacking, and a combat sequence builder.

### 1.1 Problem Statement

The current v1.0.0 engine provides foundational CSPRNG dice rolling with basic combat sequences. However, it lacks critical 10th Edition mechanics:

- **Exploding dice** (6+ triggers additional rolls)
- **Mortal wound cascades** (direct damage bypassing saves)
- **Advanced re-rolls** (re-roll 1s vs re-roll all failed)
- **Modifier stacking** (cumulative +1/-1 to hit/wound/save)
- **Declarative combat sequences** (builder pattern for complex attacks)

### 1.2 Goals

1. Implement all Warhammer 40K 10th Edition core dice mechanics
2. Maintain backward compatibility with v1.0.0 API
3. Preserve CSPRNG entropy proofs for all new roll types
4. Enable composable combat sequences via builder pattern

---

## 2. User Stories

### US-1: Exploding Dice
**As a** tactical AI engine,  
**I want** exploding dice (6+ triggers additional rolls),  
**So that** I can accurately model Sustained Hits and Lethal Hits abilities.

**Acceptance Criteria:**
- [ ] `roll(sides, explode_on=6)` generates additional rolls on trigger
- [ ] Exploded rolls can themselves explode (recursive)
- [ ] Each exploded roll has its own entropy proof
- [ ] Maximum recursion depth configurable (default: 10)
- [ ] Total value aggregates all explosions

### US-2: Mortal Wound Cascades
**As a** combat resolver,  
**I want** mortal wound generation on critical hits/wounds,  
**So that** I can model Devastating Wounds and other mortal wound sources.

**Acceptance Criteria:**
- [ ] `CombatResult` tracks `mortal_wounds` separately from regular wounds
- [ ] Mortal wounds bypass armor saves entirely
- [ ] Configurable trigger (e.g., unmodified 6 to wound)
- [ ] Mortal wound count per trigger configurable (1-6)
- [ ] Mortal wounds added to final damage without save rolls

### US-3: Advanced Re-Roll Mechanics
**As a** game engine consumer,  
**I want** granular re-roll control (re-roll 1s, re-roll all failed, re-roll successes),  
**So that** I can model abilities like "re-roll hit rolls of 1" accurately.

**Acceptance Criteria:**
- [ ] `RerollPolicy` enum: `NONE`, `ONES`, `FAILED`, `ALL`
- [ ] Separate policies for hit, wound, and save phases
- [ ] Re-rolls tracked in `CombatResult` (original + reroll)
- [ ] No "re-roll a re-roll" (each die rerolled once max)
- [ ] Entropy proofs for both original and re-rolled dice

### US-4: Modifier Stacking
**As a** tactical evaluator,  
**I want** to apply cumulative modifiers (+1/-1 to hit/wound/save),  
**So that** I can model abilities, cover, and other in-game effects.

**Acceptance Criteria:**
- [ ] `Modifiers` dataclass with `hit`, `wound`, `save` fields
- [ ] Modifiers applied after roll, before threshold comparison
- [ ] Modifiers clamped to valid range (modified roll still 1-6)
- [ ] Unmodified 1 always fails (cannot be modified to success)
- [ ] Unmodified 6 always succeeds (for wound/save phases per 10th Ed)

### US-5: Combat Sequence Builder
**As a** combat system integrator,  
**I want** a fluent builder API for complex attack sequences,  
**So that** I can declaratively define multi-phase combat with abilities.

**Acceptance Criteria:**
- [ ] `CombatSequenceBuilder` class with fluent API
- [ ] Chain: `.attacks(10).hitting(3).wounding(4).saving(5).damage(2)`
- [ ] Extend: `.with_rerolls(hit=ONES).with_modifiers(hit=+1)`
- [ ] Extend: `.with_exploding_hits(on=6).with_mortal_wounds(on=6, count=1)`
- [ ] `.execute()` returns `CombatResult` with full audit trail
- [ ] Builder validates inputs before execution

---

## 3. Clarification Cycles

### Cycle 1: Ambiguity Search

| Ambiguity | Resolution |
|-----------|------------|
| **"Exploding dice"** — Does `explode_on=6` mean *exactly* 6 or *6+* (≥6)? | **Resolution**: Use `explode_on` as threshold (≥). A roll ≥ `explode_on` triggers explosion. For modifiers, use *unmodified* roll value to check explosion trigger (10th Ed standard). |
| **"Recursive explosions"** — Can explosions explode infinitely? | **Resolution**: Yes, but capped at `max_depth=10` by default. Configurable via `roll(explode_on=6, max_explosions=10)`. |
| **"Modifier clamping"** — If roll=2 and modifier=+2, is effective roll 4 or capped at 6? | **Resolution**: Effective roll = `min(6, max(1, roll + modifier))`. Clamped to 1-6 for threshold comparison. |
| **"Unmodified 6 always succeeds"** — Does this apply to hit rolls? | **Resolution**: Per 10th Ed Core Rules, unmodified 6 always succeeds for *wound* and *save* rolls. Hit rolls do NOT auto-succeed on unmodified 6. |
| **`RerollPolicy.ALL`** — Re-roll all dice or only after seeing results? | **Resolution**: Re-roll all *failed* dice after seeing results. The "ALL" policy is distinct: re-roll every die regardless of success (rarely used, for edge cases like "re-roll all dice"). |

---

### Cycle 2: Component Impact Analysis

| Component | File | Impact | Changes Required |
|-----------|------|--------|------------------|
| **DiceRoll model** | `models.py` | LOW | Add optional `is_reroll: bool = False` field |
| **CombatResult model** | `models.py` | MEDIUM | Add `mortal_wounds: int`, `reroll_counts: dict` |
| **DiceEngine.roll()** | `engine.py` | MEDIUM | Add `explode_on: Optional[int]` parameter |
| **DiceEngine.combat_roll()** | `engine.py` | HIGH | Complete overhaul to support all new mechanics; consider deprecation in favor of builder |
| **New: RerollPolicy enum** | `models.py` | NEW | Define enum with 4 values |
| **New: Modifiers dataclass** | `models.py` | NEW | 3-field dataclass |
| **New: ExplodingRoll model** | `models.py` | NEW | Wrapper for base + explosions |
| **New: CombatSequenceBuilder** | `builder.py` | NEW | ~150 lines fluent builder |
| **Test suite** | `test_engine.py` | HIGH | Add 15+ new test cases |
| **New: test_builder.py** | `tests/` | NEW | Builder-specific tests |

**Dependency Graph:**
```
models.py (RerollPolicy, Modifiers, ExplodingRoll)
    ↓
engine.py (roll_exploding, modifier logic)
    ↓
builder.py (CombatSequenceBuilder)
    ↓
tests/ (comprehensive coverage)
```

---

### Cycle 3: Edge Case & Failure Analysis

| Edge Case | Behavior | Test Scenario |
|-----------|----------|---------------|
| **Exploding with max_depth=0** | No explosions allowed; behaves like normal roll | `roll(6, explode_on=6, max_explosions=0)` returns single roll |
| **Modifier overflows** | Clamp to 1-6 | Roll=1, modifier=+10 → effective=6 (capped) |
| **Modifier underflows** | Clamp to 1-6 | Roll=6, modifier=-10 → effective=1 (capped) |
| **Unmodified 1 with +5 modifier** | Still fails (1 always fails before modifier) | Roll=1, modifier=+5, hit_on=3 → MISS |
| **Unmodified 6 on wound with -5 modifier** | Still succeeds (10th Ed rule) | Roll=6, modifier=-5, wound_on=4 → WOUND |
| **Zero attacks** | Return empty CombatResult | `builder.attacks(0).execute()` → 0 damage |
| **Negative damage** | Raise ValueError | `builder.damage(-1)` raises immediately |
| **Missing required builder fields** | Raise ValueError on execute() | `.attacks(5).execute()` without hitting/wounding |
| **Mortal wounds on 6 with wound_on=6** | Both trigger (mortal AND regular wound) | Roll=6 on wound phase → +1 wound AND +1 mortal |
| **All rolls fail** | Return 0 damage, valid result | 10 attacks, all miss → valid CombatResult |
| **Division by zero in batch average** | Return 0.0 average | `roll_batch(0)` → average=0.0 |

**Failure Modes:**
| Failure | Detection | Recovery |
|---------|-----------|----------|
| Infinite explosion loop | `max_explosions` counter | Cap and log warning |
| Invalid modifier type | Type checking in `Modifiers.__post_init__` | Raise `TypeError` |
| Invalid reroll policy | Enum validation | Raise `ValueError` |
| Missing seed in test mode | Deterministic seed detection | Fallback to CSPRNG |

---

## 4. Technical Scope

### 4.1 Affected Files

| File | Change Type | Description |
|------|------------|-------------|
| `src/dice_engine/models.py` | MODIFY | Add `RerollPolicy`, `Modifiers`, `ExplodingRoll`, update `CombatResult` |
| `src/dice_engine/engine.py` | MODIFY | Add `roll_exploding()`, modifier logic in combat phases |
| `src/dice_engine/builder.py` | NEW | `CombatSequenceBuilder` class (~150 lines) |
| `src/dice_engine/__init__.py` | MODIFY | Export new classes |
| `tests/test_engine.py` | MODIFY | Add 15+ test cases for new mechanics |
| `tests/test_builder.py` | NEW | Builder pattern tests (~100 lines) |

### 4.2 API Additions

```python
# New Enums
class RerollPolicy(Enum):
    NONE = "none"
    ONES = "ones"      # Re-roll 1s only
    FAILED = "failed"  # Re-roll all failed
    ALL = "all"        # Re-roll all (rarely used)

# New Dataclasses
@dataclass
class Modifiers:
    hit: int = 0   # +1/-1 to hit
    wound: int = 0 # +1/-1 to wound
    save: int = 0  # +1/-1 to save
    
    def __post_init__(self):
        for field in (self.hit, self.wound, self.save):
            if not isinstance(field, int):
                raise TypeError(f"Modifier must be int, got {type(field)}")

@dataclass
class ExplodingRoll:
    base_roll: DiceRoll
    explosions: list[DiceRoll] = field(default_factory=list)
    
    @property
    def total(self) -> int:
        return self.base_roll.value + sum(r.value for r in self.explosions)
    
    @property
    def count(self) -> int:
        return 1 + len(self.explosions)

# Updated CombatResult
@dataclass
class CombatResult:
    # ... existing fields ...
    mortal_wounds: int = 0
    hit_rerolls: int = 0
    wound_rerolls: int = 0
    save_rerolls: int = 0

# Builder Pattern
class CombatSequenceBuilder:
    def attacks(self, count: int) -> Self
    def hitting(self, target: int) -> Self
    def wounding(self, target: int) -> Self
    def saving(self, target: int) -> Self
    def damage(self, per_wound: int) -> Self
    def with_rerolls(self, hit=NONE, wound=NONE, save=NONE) -> Self
    def with_modifiers(self, mods: Modifiers) -> Self
    def with_exploding_hits(self, on: int = 6, extra: int = 1) -> Self
    def with_mortal_wounds(self, on: int = 6, count: int = 1) -> Self
    def execute(self) -> CombatResult
    def validate(self) -> list[str]  # Return validation errors
```

### 4.3 Backward Compatibility

The existing `combat_roll()` method signature remains unchanged. New functionality is additive via:
- Optional parameters on existing methods
- New `CombatSequenceBuilder` for advanced use
- New model fields with default values

---

## 5. Non-Goals

The following are explicitly **out of scope** for v1.1.0:

1. ❌ Invulnerable saves (separate save roll system) — v1.2.0
2. ❌ Feel No Pain rolls — v1.2.0
3. ❌ Monte Carlo simulation — v1.2.0
4. ❌ Variance/probability analysis — v1.2.0
5. ❌ Multi-weapon attack sequences — v1.2.0

---

## 6. Dependencies

| Dependency | Type | Notes |
|------------|------|-------|
| Entropy-Buffer | Runtime | Entropy source (already integrated) |
| secrets (stdlib) | Runtime | CSPRNG (already used) |
| Python 3.11+ | Runtime | For `typing.Self` in builder |

---

## 7. Risks and Mitigations

| Risk | Impact | Mitigation |
|------|--------|------------|
| Exploding dice infinite loop | High | Max recursion depth (default 10) |
| Modifier stacking rules misinterpretation | Medium | Reference 10th Ed Core Rules |
| Backward compatibility break | High | All new fields have defaults |
| Entropy proof bloat in explosions | Low | Proofs remain 16-char hex |

---

## 8. Success Metrics

| Metric | Target | Measurement |
|--------|--------|-------------|
| **Test Coverage** | 90%+ for new code | pytest-cov |
| **Backward Compat** | 100% existing tests pass | CI |
| **Performance** | <5% regression | Benchmark |
| **Mechanic Accuracy** | 100% 10th Ed rules | Manual audit |

---

*Spec-Bot • `/speckit.clarify` complete (3/3 cycles)*
