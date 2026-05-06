# claw-sync — Synchronization

**crates.io:** [crates.io/crates/claw-sync](https://crates.io/crates/claw-sync) · v0.1.2

Local-first CRDT sync with cryptographic integrity.

## Security model

| Layer | Technology |
|---|---|
| Payload encryption | XSalsa20-Poly1305 |
| Message signing | Ed25519 |
| Integrity hashing | BLAKE3 |
| Causal ordering | Hybrid Logical Clocks (HLC) |
| Audit chaining | BLAKE3-chained append-only log |

## Offline queue

When the hub is unreachable, chunks queue in local SQLite. The drain loop uses exponential backoff with jitter (base 200ms, cap 30s). After `max_retries`, chunks are marked `failed` and visible in `clawdb status`.

## Conflict resolution

Two concurrent writes to the same field create a `sync_conflicts` row. Options:
- `LastWrite` — automatic, takes the later HLC timestamp
- `resolve_manual(conflict_id, chosen_value)` — explicit resolution

## Audit chain verification

```bash
clawdb sync verify-audit
# ✓ Audit chain verified — 1,247 rows intact
```
