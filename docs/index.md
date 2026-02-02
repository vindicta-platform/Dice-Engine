# Dice Engine

**Casino-grade randomness for game mechanics.**

Dice Engine provides cryptographically secure random number generation with auditable entropy proofs — never trust a dice roll again.

## Why Dice Engine?

- **CSPRNG Only** — No `random.random()`, ever
- **No Modulo Bias** — Rejection sampling for fair rolls
- **Entropy Proofs** — Audit any roll after the fact
- **Async-Safe** — Thread-safe buffer management

## Installation

```bash
uv pip install git+https://github.com/vindicta-platform/Dice-Engine.git
```

## Quick Roll

```python
from dice_engine import roll

result = roll("2d6")
print(result.total)       # 7
print(result.proof)       # a1b2c3...
```

---

[Full Platform](https://vindicta-platform.github.io/mkdocs/)
