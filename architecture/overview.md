# Architecture Overview

ClawDB is a modular Rust runtime composed of seven published crates. Each crate is independently usable, but together they form a single cognitive database.

```
                    ┌─────────────────────┐
                    │       clawdb        │
                    │   Unified Runtime   │
                    └──────────┬──────────┘
                               │
     ┌─────────┬───────────────┼──────────────┬──────────┐
     │         │               │              │          │
┌────▼───┐ ┌───▼────┐ ┌───────▼──┐ ┌────────▼─┐ ┌──────▼───┐
│  core  │ │ vector │ │  branch  │ │  reflect │ │   sync   │
└────────┘ └────────┘ └──────────┘ └──────────┘ └──────────┘
                               │
                         ┌─────▼─────┐
                         │   guard   │
                         └───────────┘
```

## Crate inventory

| Crate | Language | Purpose |
|---|---|---|
| `claw-core` | Rust | Embedded SQLite storage, WAL, FTS5, LRU cache |
| `claw-vector` | Rust + Python | HNSW vector index, hybrid search, embedding service |
| `claw-guard` | Rust | JWT sessions, policy engine, data masking, audit log |
| `claw-branch` | Rust | Snapshot branching, DAG lineage, merge strategies |
| `claw-reflect` | Python | 7-stage distillation pipeline, LLM-powered fact extraction |
| `claw-sync` | Rust | CRDT replication, HLC ordering, encrypted delta sync |
| `clawdb` | Rust | Aggregate runtime — wires all components into one API |

## Local-first guarantee

All read/write operations complete against local SQLite first. Vector indexing, reflect, and sync are all non-blocking — if they fail, the core operation still succeeds.

See individual crate pages for deep-dive documentation.
