# API

## roll()

```python
def roll(notation: str) -> RollResult
```

## RollResult

```python
class RollResult:
    dice: list[int]
    modifier: int
    total: int
    proof: str
```

## verify_roll()

```python
def verify_roll(total: int, proof: str) -> bool
```
