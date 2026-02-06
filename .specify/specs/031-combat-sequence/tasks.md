# Tasks: Combat Sequence Builder

**Input**: specs/031-combat-sequence/ | **Prerequisites**: spec.md, plan.md

## Phase 1: Setup

- [ ] T001 Create `src/combat/` directory

---

## Phase 2: Foundational

- [ ] T002 Define CombatSequence model
- [ ] T003 [P] Define CombatResult model
- [ ] T004 [P] Define modifier types

---

## Phase 3: User Story 1 - Build Combat Sequence (P1) 🎯 MVP

- [ ] T005 [US1] Implement CombatBuilder class
- [ ] T006 [US1] Add `hit_roll()` method
- [ ] T007 [US1] Add `wound_roll()` method
- [ ] T008 [US1] Add `save_roll()` method
- [ ] T009 [US1] Add `execute()` method
- [ ] T010 [US1] Track modifiers throughout chain

---

## Phase 4: Polish

- [ ] T011 [P] Optimize for <50ms execution
- [ ] T012 [P] Write combat sequence tests
