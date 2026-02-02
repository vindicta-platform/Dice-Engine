# Entropy Proofs

## What Are They?

Every roll produces a cryptographic proof that can verify:
- The exact entropy bytes used
- The rejection sampling process
- The final result derivation

## Verifying a Roll

```python
from dice_engine import verify_roll

is_valid = verify_roll(result.total, result.proof)
```

## Format

```
PROOF: v1|entropy_hex|notation|result|timestamp
```

## Why This Matters

- **No cheating** — Cannot fake rolls
- **Audit trail** — Replay any game
- **Trust** — Opponents can verify
