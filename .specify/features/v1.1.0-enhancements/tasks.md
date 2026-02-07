# Dice-Engine v1.1.0 Enhancements — Tasks

> **Spec**: [spec.md](./spec.md)  
> **Plan**: [plan.md](./plan.md)  
> **Status**: SYNCED TO GITHUB

---

## Task List

### T001: Add RerollPolicy Enum
- **File**: `src/dice_engine/models.py`
- **Type**: NEW CODE
- **Effort**: XS (< 30 min)
- **Dependencies**: None
- **GitHub Issue**: [#2](https://github.com/vindicta-platform/Dice-Engine/issues/2)

---

### T002: Add Modifiers Dataclass
- **File**: `src/dice_engine/models.py`
- **Type**: NEW CODE
- **Effort**: XS (< 30 min)
- **Dependencies**: None
- **GitHub Issue**: [#3](https://github.com/vindicta-platform/Dice-Engine/issues/3)

---

### T003: Add ExplodingRoll Dataclass
- **File**: `src/dice_engine/models.py`
- **Type**: NEW CODE
- **Effort**: S (30-60 min)
- **Dependencies**: T001
- **GitHub Issue**: [#4](https://github.com/vindicta-platform/Dice-Engine/issues/4)

---

### T004: Update CombatResult with Mortal Wounds
- **File**: `src/dice_engine/models.py`
- **Type**: MODIFY
- **Effort**: XS (< 30 min)
- **Dependencies**: None
- **GitHub Issue**: [#5](https://github.com/vindicta-platform/Dice-Engine/issues/5)

---

### T005: Implement roll_exploding() Method
- **File**: `src/dice_engine/engine.py`
- **Type**: NEW CODE
- **Effort**: M (1-2 hrs)
- **Dependencies**: T003
- **GitHub Issue**: [#6](https://github.com/vindicta-platform/Dice-Engine/issues/6)

---

### T006: Add Modifier Application Helpers
- **File**: `src/dice_engine/engine.py`
- **Type**: NEW CODE
- **Effort**: S (30-60 min)
- **Dependencies**: T002
- **GitHub Issue**: [#7](https://github.com/vindicta-platform/Dice-Engine/issues/7)

---

### T007: Create CombatSequenceBuilder Class
- **File**: `src/dice_engine/builder.py` (NEW)
- **Type**: NEW FILE
- **Effort**: L (2-4 hrs)
- **Dependencies**: T001, T002, T005, T006
- **GitHub Issue**: [#8](https://github.com/vindicta-platform/Dice-Engine/issues/8)

---

### T008: Update Package Exports
- **File**: `src/dice_engine/__init__.py`
- **Type**: MODIFY
- **Effort**: XS (< 30 min)
- **Dependencies**: T001, T002, T003, T007
- **GitHub Issue**: [#9](https://github.com/vindicta-platform/Dice-Engine/issues/9)

---

### T009: Add Exploding Dice Tests
- **File**: `tests/test_engine.py`
- **Type**: NEW CODE
- **Effort**: S (30-60 min)
- **Dependencies**: T005
- **GitHub Issue**: [#10](https://github.com/vindicta-platform/Dice-Engine/issues/10)

---

### T010: Add Modifier Tests
- **File**: `tests/test_engine.py`
- **Type**: NEW CODE
- **Effort**: S (30-60 min)
- **Dependencies**: T006
- **GitHub Issue**: [#11](https://github.com/vindicta-platform/Dice-Engine/issues/11)

---

### T011: Add Reroll Policy Tests
- **File**: `tests/test_engine.py`
- **Type**: NEW CODE
- **Effort**: S (30-60 min)
- **Dependencies**: T001
- **GitHub Issue**: [#12](https://github.com/vindicta-platform/Dice-Engine/issues/12)

---

### T012: Create Builder Test Suite
- **File**: `tests/test_builder.py` (NEW)
- **Type**: NEW FILE
- **Effort**: M (1-2 hrs)
- **Dependencies**: T007
- **GitHub Issue**: [#13](https://github.com/vindicta-platform/Dice-Engine/issues/13)

---

## Dependency Graph

```
T001 (#2) ─┬─→ T007 (#8)
T002 (#3) ─┤
T003 (#4) ─┘
    ↓
T005 (#6) ─→ T007
T006 (#7) ─→ T007
    ↓
T008 (#9)
    ↓
T009–T012 (#10-#13)
```

---

## Effort Summary

| Effort | Count | Total Time |
|--------|-------|------------|
| XS | 4 | ~2 hrs |
| S | 5 | ~3-5 hrs |
| M | 2 | ~2-4 hrs |
| L | 1 | ~2-4 hrs |
| **Total** | **12** | **~9-15 hrs** |

---

*Spec-Bot • `/speckit.taskstoissues` complete*
