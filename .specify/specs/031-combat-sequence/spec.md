# Feature Specification: Combat Sequence Builder

**Feature Branch**: `031-combat-sequence`  
**Created**: 2026-02-06  
**Status**: Draft  
**Target**: Week 3 | **Repository**: Dice-Engine

## User Scenarios & Testing

### User Story 1 - Build Combat Sequence (Priority: P1)

System provides fluent API for combat resolution.

**Acceptance Scenarios**:
1. **Given** combat builder, **When** chained methods called, **Then** sequence built
2. **Given** complete sequence, **When** execute called, **Then** results calculated

---

## Requirements

### Functional Requirements
- **FR-001**: API MUST support fluent builder pattern
- **FR-002**: API MUST chain hit/wound/save rolls
- **FR-003**: API MUST track all modifiers

### Key Entities
- **CombatSequence**: steps[], modifiers[]
- **CombatResult**: wounds, mortalWounds, totalDamage

## Success Criteria
- **SC-001**: Full attack sequence in <50ms
- **SC-002**: All modifiers applied correctly
