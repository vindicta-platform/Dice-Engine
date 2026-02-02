# Getting Started

## Installation

```bash
uv pip install git+https://github.com/vindicta-platform/Dice-Engine.git
```

## Basic Usage

```python
from dice_engine import roll, DiceNotation

# Simple roll
result = roll("2d6")

# With modifier
result = roll("3d6+5")

# Multiple dice types
result = roll("2d6 + 1d8")
```

## Notation Format

| Notation | Meaning |
|----------|---------|
| `2d6` | Roll 2 six-sided dice |
| `3d6+5` | Roll 3d6, add 5 |
| `1d20-2` | Roll 1d20, subtract 2 |
