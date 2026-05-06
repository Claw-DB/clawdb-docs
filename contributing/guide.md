# Contributing Guide

ClawDB is Apache-2.0 licensed and developed in the open. Contributions are welcome.

## Repository structure

```
GitHub org: Claw-DB
  ClawDB/       — aggregate runtime (clawdb, clawdb-server, clawdb-cli)
  claw-core/    — storage engine
  claw-vector/  — semantic search
  claw-guard/   — policy engine
  claw-branch/  — branching
  claw-reflect/ — summarization service
  claw-sync/    — synchronization
  claw-sdk/     — TypeScript, Python, Go, Rust client SDKs
  claw-cloud/   — managed cloud platform
```

## Development setup

```bash
# Clone the repo you want to work on
git clone https://github.com/Claw-DB/claw-core
cd claw-core

# Rust crates
cargo build
cargo test --all-features
cargo clippy --all-targets -- -D warnings
cargo fmt --all

# Run deny checks
cargo deny check

# Python (claw-reflect)
pip install -e ".[dev]"
pytest tests/
mypy claw_reflect --strict
ruff check .
```

## PR checklist

Before submitting a pull request:

- [ ] `cargo test --all-features` passes
- [ ] `cargo clippy -- -D warnings` passes with 0 warnings
- [ ] `cargo fmt --check` passes
- [ ] `cargo deny check` passes
- [ ] New public items have `///` doc comments
- [ ] New features have tests
- [ ] CHANGELOG.md updated
- [ ] If the PR changes the public API, it notes whether this is a breaking change

## Commit message format

```
type(scope): short description

Longer explanation if needed.
```

Types: `feat`, `fix`, `docs`, `refactor`, `test`, `chore`

## Security vulnerabilities

Do not file public issues for security vulnerabilities. Email security@clawdb.dev.
