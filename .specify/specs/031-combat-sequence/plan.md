# Implementation Plan: Combat Sequence Builder

**Branch**: `031-combat-sequence` | **Date**: 2026-02-06 | **Spec**: [spec.md](./spec.md)

## Summary

Fluent builder API for combat resolution sequences. Chains hit/wound/save rolls with modifier tracking.

## Technical Context

**Language/Version**: Python 3.11  
**Primary Dependencies**: Entropy-Buffer  
**Storage**: N/A  
**Testing**: pytest  
**Target Platform**: Dice-Engine  
**Project Type**: Backend library  

## Project Structure

```text
Dice-Engine/src/
└── combat/
    ├── builder.py           # [NEW] Combat builder
    ├── sequence.py          # [NEW] Sequence execution
    └── modifiers.py         # [NEW] Modifier handling
```
