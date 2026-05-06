# claw-vector — Semantic Search

**crates.io:** [crates.io/crates/claw-vector](https://crates.io/crates/claw-vector) · v0.1.2

Hybrid semantic search combining HNSW vector indexing with FTS5 keyword search.

## Index auto-promotion

| Collection size | Index type | Notes |
|---|---|---|
| < 1,000 vectors | Flat | 100% recall, exact nearest-neighbour |
| ≥ 1,000 vectors | HNSW | ~95% recall, 10–100× faster |

ClawDB promotes automatically — no configuration needed.

## Search pipeline

1. Embed query → HNSW ANN search → FTS5 keyword search (parallel)
2. Merge with Reciprocal Rank Fusion (RRF)
3. Apply metadata filter
4. Optional reranking (diversity, recency)

## Workspace isolation

All collections include `workspace_id` in every query. Cross-workspace leakage is impossible at the SQL layer.

## Atomic index persistence

```
<id>.idx.tmp  →  rename  →  <id>.idx  +  <id>.idx.manifest (BLAKE3)
```
Corrupt indexes are detected on load and rebuilt from SQLite metadata.
