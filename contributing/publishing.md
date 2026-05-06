# Crate Publishing

When to publish and how.

## Version policy

All ClawDB crates follow Semantic Versioning:

| Change | Bump |
|---|---|
| Bug fix, no API change | patch (0.1.0 → 0.1.1) |
| New method, backwards-compatible | minor (0.1.0 → 0.2.0) |
| Breaking API change | major (0.1.0 → 1.0.0) |

Until v1.0, all crates are versioned together — a release bumps all crates to the same version simultaneously.

## Publish order

Crates must be published in dependency order (dependencies before dependents):

```
Tier 1: claw-core
Tier 2: claw-vector, claw-guard, claw-branch
Tier 3: claw-sync, claw-reflect-client
Tier 4: clawdb, clawdb-server, clawdb-cli
```

## Publishing

Publishing is automated via GitHub Actions on version tags:

```bash
# 1. Bump version in all Cargo.toml files
# 2. Update CHANGELOG.md
# 3. Commit and tag
git tag v0.1.3
git push origin main --tags
# GitHub Actions publishes all crates in order
```

## Pre-publish checklist

Run this before tagging:

```bash
cargo publish --dry-run -p claw-core   # tier 1 first
# fix any errors, then:
git tag v0.1.X
git push --tags
```
