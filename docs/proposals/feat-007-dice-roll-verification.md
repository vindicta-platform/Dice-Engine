# Feature Proposal: Dice Roll Replay & Verification

**Proposal ID**: FEAT-007
**Author**: Unified Product Architect (Autonomous)
**Created**: 2026-02-01
**Status**: Draft
**Priority**: High
**Target Repository**: Dice-Engine

---

## Part A: Software Design Document (SDD)

### 1. Executive Summary

Add roll replay and cryptographic verification capabilities to the Dice-Engine, enabling players to verify the fairness of past rolls and replay specific roll sequences for dispute resolution.

### 2. System Architecture

#### 2.1 Current State
- CSPRNG-backed random generation
- Rejection sampling for unbiased rolls
- Basic entropy proofs
- No replay capability

#### 2.2 Proposed Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                    Dice Engine + Verification                   │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │                   Roll Request                          │    │
│  │   dice: "2d6+4"  seed: (optional)                        │    │
│  └─────────────────────────────────────────────────────────┘    │
│                              │                                  │
│                              ▼                                  │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │                   Entropy Source                        │    │
│  │   ┌─────────────┐    ┌─────────────┐                    │    │
│  │   │   CSPRNG    │ OR │ Seed Replay │                    │    │
│  │   │  (os.urandom)│   │  (SHA-256)  │                    │    │
│  │   └─────────────┘    └─────────────┘                    │    │
│  └─────────────────────────────────────────────────────────┘    │
│                              │                                  │
│                              ▼                                  │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │                  Verifiable Result                      │    │
│  │   result: [4, 2]  proof: {seed_hash, entropy_used}     │    │
│  └─────────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────────┘
```

#### 2.3 File Changes

```
Dice-Engine/
├── src/
│   └── dice_engine/
│       ├── verifier.py          [NEW] Roll verification
│       ├── replay.py            [NEW] Deterministic replay
│       ├── proof.py             [MODIFY] Enhanced proofs
│       └── engine.py            [MODIFY] Seed support
├── tests/
│   ├── test_verifier.py         [NEW]
│   └── test_replay.py           [NEW]
└── docs/
    └── verification.md          [NEW]
```

### 3. Verification System

```python
@dataclass
class RollProof:
    """Cryptographic proof of a dice roll."""
    roll_id: str               # Unique roll identifier
    timestamp: datetime
    dice_notation: str         # "2d6+4"
    result: list[int]          # [4, 2]
    seed_commitment: str       # SHA-256 of seed (pre-committed)
    seed_reveal: str           # Actual seed (revealed after roll)
    entropy_used: bytes        # Raw entropy consumed
    signature: str             # HMAC of above fields

class DiceVerifier:
    """Verify the integrity of past rolls."""

    def verify(self, proof: RollProof) -> VerificationResult:
        """Verify a roll proof is valid and untampered."""

    def replay(self, seed: str, dice_notation: str) -> list[int]:
        """Replay a roll with known seed."""
```

### 4. Commitment Scheme

For tournament-critical rolls:
1. **Commit Phase**: Server publishes `SHA-256(seed)` before roll
2. **Roll Phase**: Roll is executed with that seed
3. **Reveal Phase**: Seed is revealed, anyone can verify

### 5. Security Considerations

- Seeds never reused
- Commitment prevents server manipulation
- Client can provide partial entropy
- Proofs stored immutably (Audit-Log-Pro integration)

---

## Part B: Behavior Driven Development (BDD)

### User Stories

#### US-001: Verify Past Roll
**As a** tournament player
**I want to** verify that a critical roll was fair
**So that** I can trust the platform's randomness

#### US-002: Replay Roll Sequence
**As a** dispute resolver
**I want to** replay a contested roll sequence
**So that** I can confirm or refute a player's claim

#### US-003: Export Roll Proof
**As a** content creator
**I want to** export verifiable roll proofs
**So that** my audience can trust my battle reports

### Acceptance Criteria

```gherkin
Feature: Dice Roll Verification

  Scenario: Verify a single roll
    Given a roll was made with proof ID "roll-123"
    When I submit the proof for verification
    Then the verifier should confirm the roll is valid
    And show the original seed and entropy used

  Scenario: Detect tampered proof
    Given a proof with a modified result
    When I submit the tampered proof for verification
    Then the verifier should return "INVALID"
    And indicate which field was tampered

  Scenario: Replay deterministic sequence
    Given a known seed "abc123"
    When I replay roll "3d6" with that seed
    Then I should get the same result every time
    And it should match the original roll's result
```

---

## Implementation Estimate

| Phase | Effort | Dependencies |
|-------|--------|--------------|
| Proof Structure | 3 hours | None |
| Verifier | 4 hours | None |
| Replay Engine | 4 hours | None |
| Commitment Scheme | 3 hours | None |
| Testing | 4 hours | None |
| **Total** | **18 hours** | |
