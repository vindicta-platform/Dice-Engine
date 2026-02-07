# Feature Specification: Exploding Dice Support

**Feature Branch**: `030-exploding-dice`  
**Created**: 2026-02-06  
**Status**: Draft  
**Target**: Week 2 | **Repository**: Dice-Engine

## User Scenarios & Testing

### User Story 1 - Roll Exploding Dice (Priority: P1)

System supports dice that re-roll on max value.

**Acceptance Scenarios**:
1. **Given** exploding dice config, **When** max rolled, **Then** additional roll added
2. **Given** exploding roll, **When** results returned, **Then** all rolls tracked

---

## Requirements

### Functional Requirements
- **FR-001**: Engine MUST support exploding dice mechanic
- **FR-002**: Engine MUST track explosion chain
- **FR-003**: Engine MUST support configurable explosion threshold

### Key Entities
- **ExplodingRoll**: base, explosions[], total

## Success Criteria
- **SC-001**: Explosion chains handled correctly
- **SC-002**: Fair distribution maintained
