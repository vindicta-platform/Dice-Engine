# Implementation Plan: Exploding Dice Support

**Branch**: `030-exploding-dice` | **Date**: 2026-02-06 | **Spec**: [spec.md](./spec.md)

## Summary

Exploding dice mechanic where max values trigger re-rolls. Tracks explosion chains and supports configurable thresholds.

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
└── mechanics/
    └── exploding.py         # [NEW] Exploding dice
```
