# claw-branch — Branching

**crates.io:** [crates.io/crates/claw-branch](https://crates.io/crates/claw-branch) · v0.1.2

Fork memory state, simulate decisions, merge what worked.

## Merge strategies

| Strategy | Behaviour |
|---|---|
| `LastWrite` | Keep the more recently updated record (default) |
| `SourceWins` | Always take source branch version |
| `TargetWins` | Always keep target version |
| `Manual` | Pause on conflicts, emit `ConflictRecord` for resolution |
| `Union` | Set union for arrays; recursive timestamp merge for objects |

## Snapshot safety

All snapshots use SQLite's Online Backup API (not file copy), ensuring WAL pages are flushed before the snapshot is taken. Each snapshot has a BLAKE3 sidecar verified on load.

## Name validation

Branch names must match `^[a-zA-Z0-9_/.-]{1,128}$`. Names containing `..` or starting with `/` are rejected before any filesystem operation.
