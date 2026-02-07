# Dice-Engine

CSPRNG-backed dice roller with entropy proofs for game mechanics.

## Overview

Dice-Engine provides cryptographically secure random number generation with rejection sampling and auditable entropy proofs.

## Installation

```bash
uv pip install git+https://github.com/vindicta-platform/Dice-Engine.git
```

## Features

- CSPRNG-backed randomness
- Rejection sampling (no modulo bias)
- Entropy proofs for auditing
- Async-safe buffer management

## Platform Documentation

> **📌 Important:** All cross-cutting decisions, feature proposals, and platform-wide architecture documentation live in [**Platform-Docs**](https://github.com/vindicta-platform/Platform-Docs).
>
> Any decision affecting multiple repos **must** be recorded there before implementation.

- 📋 [Feature Proposals](https://github.com/vindicta-platform/Platform-Docs/tree/main/docs/proposals)
- 🏗️ [Architecture Decisions](https://github.com/vindicta-platform/Platform-Docs/tree/main/docs)
- 📖 [Contributing Guide](https://github.com/vindicta-platform/Platform-Docs/blob/main/CONTRIBUTING.md)

## License

MIT License - See [LICENSE](./LICENSE) for details.
