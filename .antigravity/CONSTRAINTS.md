# Dice-Engine Constraints

> Critical rules agents MUST follow when modifying this repository.

## ⛔ Hard Constraints

1. **CSPRNG Only** - Never use `random` module, only `secrets`
2. **Deterministic Replay** - Same seed must produce same sequence
3. **Proof Chain Integrity** - Every roll must be provable
4. **No External APIs** - Entropy must be local

## 🎲 Dice Rules

### Notation Syntax
```
XdY[+|-]Z[!][r]
X = count (1-100)
Y = sides (2, 3, 4, 6, 8, 10, 12, 20, 100)
Z = modifier
! = exploding
r = re-roll ones
```

### Validation
- Reject invalid dice expressions
- Cap at 100 dice per roll
- Cap at d100 maximum sides

## 🔐 Proof Requirements

### Commitment Scheme
```python
# Before roll
commitment = sha256(seed + nonce)
# After roll
reveal = (seed, nonce, results)
# Verification
verify(commitment, reveal) -> bool
```

### Audit Trail
Every roll stores:
- Timestamp (UTC)
- Commitment hash
- Reveal data
- Game context ID

## 🧪 Testing Requirements

Before merging:
- [ ] `pytest` passes  
- [ ] Statistical distribution tests pass
- [ ] Proof verification tests pass
- [ ] No use of `random` module (verified via grep)
