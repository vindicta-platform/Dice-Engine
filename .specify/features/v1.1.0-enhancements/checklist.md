# Dice-Engine v1.1.0 Enhancements — Requirements Checklist

> **Spec**: [spec.md](./spec.md)  
> **Purpose**: English "unit tests" to verify spec compliance

---

## US-1: Exploding Dice

- [ ] **CHK-001**: Calling `roll(6, explode_on=6)` with a result of 6 generates at least one additional roll
- [ ] **CHK-002**: Each exploded roll has its own unique entropy proof
- [ ] **CHK-003**: Explosions can recursively explode (6 → 6 → 5 = 3 rolls total)
- [ ] **CHK-004**: Setting `max_explosions=0` prevents any explosions
- [ ] **CHK-005**: Default max explosions is 10 (prevents infinite loops)
- [ ] **CHK-006**: `ExplodingRoll.total` correctly sums base + all explosions
- [ ] **CHK-007**: `ExplodingRoll.count` returns 1 + number of explosions

---

## US-2: Mortal Wound Cascades

- [ ] **CHK-008**: `CombatResult.mortal_wounds` field exists with default 0
- [ ] **CHK-009**: Mortal wounds trigger on specified unmodified roll (e.g., 6 to wound)
- [ ] **CHK-010**: Mortal wounds bypass all save rolls
- [ ] **CHK-011**: Mortal wound count per trigger is configurable (1-6)
- [ ] **CHK-012**: Mortal wound damage is added to `damage_dealt`

---

## US-3: Advanced Re-Roll Mechanics

- [ ] **CHK-013**: `RerollPolicy.NONE` does not re-roll any dice
- [ ] **CHK-014**: `RerollPolicy.ONES` re-rolls only dice showing 1
- [ ] **CHK-015**: `RerollPolicy.FAILED` re-rolls all failed dice
- [ ] **CHK-016**: `RerollPolicy.ALL` re-rolls all dice regardless of result
- [ ] **CHK-017**: Re-rolled dice cannot be re-rolled again (once max)
- [ ] **CHK-018**: `CombatResult.hit_rerolls` tracks number of hit re-rolls
- [ ] **CHK-019**: `CombatResult.wound_rerolls` tracks wound re-rolls
- [ ] **CHK-020**: `CombatResult.save_rerolls` tracks save re-rolls
- [ ] **CHK-021**: Both original and re-roll have entropy proofs

---

## US-4: Modifier Stacking

- [ ] **CHK-022**: `Modifiers(hit=1)` adds +1 to hit rolls
- [ ] **CHK-023**: `Modifiers(wound=-1)` subtracts 1 from wound rolls
- [ ] **CHK-024**: Modified roll is clamped between 1 and 6
- [ ] **CHK-025**: Roll of 1 + modifier +5 still fails (unmodified 1 rule)
- [ ] **CHK-026**: Roll of 6 + modifier -5 still succeeds on wound/save (unmodified 6 rule)
- [ ] **CHK-027**: Unmodified 6 does NOT auto-succeed on hit rolls (10th Ed specific)

---

## US-5: Combat Sequence Builder

- [ ] **CHK-028**: `CombatSequenceBuilder` is importable from `dice_engine`
- [ ] **CHK-029**: `.attacks(10).hitting(3).wounding(4).saving(5).damage(2)` chains correctly
- [ ] **CHK-030**: `.with_rerolls(hit=RerollPolicy.ONES)` applies reroll policy
- [ ] **CHK-031**: `.with_modifiers(Modifiers(hit=1))` applies modifiers
- [ ] **CHK-032**: `.with_exploding_hits(on=6)` enables exploding dice
- [ ] **CHK-033**: `.with_mortal_wounds(on=6, count=1)` enables mortal wounds
- [ ] **CHK-034**: `.execute()` returns a valid `CombatResult`
- [ ] **CHK-035**: `.validate()` returns list of errors for missing required fields
- [ ] **CHK-036**: Missing `attacks()` raises error on execute

---

## Edge Cases

- [ ] **CHK-037**: Zero attacks returns empty `CombatResult` with 0 damage
- [ ] **CHK-038**: Negative damage raises `ValueError`
- [ ] **CHK-039**: All rolls failing returns valid result with 0 damage
- [ ] **CHK-040**: `roll_batch(0)` returns average of 0.0 (no division by zero)

---

## Backward Compatibility

- [ ] **CHK-041**: Existing `combat_roll()` signature still works without changes
- [ ] **CHK-042**: All existing tests in `test_engine.py` pass
- [ ] **CHK-043**: `DiceRoll`, `CombatResult`, `BatchRollResult` are still importable

---

## Summary

| Category | Checks |
|----------|--------|
| US-1: Exploding Dice | 7 |
| US-2: Mortal Wounds | 5 |
| US-3: Re-Roll | 9 |
| US-4: Modifiers | 6 |
| US-5: Builder | 9 |
| Edge Cases | 4 |
| Backward Compat | 3 |
| **Total** | **43** |

---

*Spec-Bot • `/speckit.checklist` complete*
