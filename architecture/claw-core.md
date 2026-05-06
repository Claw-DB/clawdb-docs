# claw-core — Storage Engine

**crates.io:** [crates.io/crates/claw-core](https://crates.io/crates/claw-core) · v0.1.2

The embedded local database engine. Every memory lives here first.

## Key properties

- **WAL mode** — concurrent reads, crash-safe writes
- **LRU cache** — sub-100µs hot reads
- **FTS5** — full-text search, atomically synced with memory inserts
- **BLAKE3 snapshots** — verified with constant-time hash comparison

## Standalone usage

```rust
use claw_core::{ClawEngine, ClawConfig, MemoryType};

let engine = ClawEngine::open(ClawConfig {
    db_path: "~/.clawdb/main.db".into(),
    ..Default::default()
}).await?;

let id = engine.insert_memory("content", MemoryType::Fact, &["tag1"], json!({})).await?;
let hits = engine.fts_search("query", 10).await?;
let stats = engine.stats().await?;  // total_memories, cache_hit_rate, db_size_bytes
```
