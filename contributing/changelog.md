# Changelog

## v0.1.2 — May 2026

### Changes
- Fixed WAL checkpoint race condition in claw-core snapshot
- Added workspace isolation to claw-vector (all collections now scoped by workspace_id)
- Fixed composite reranker score normalisation (was causing incorrect ranking when chaining passes)
- Added BLAKE3 sidecar verification on claw-branch index load
- claw-guard: implemented full policy engine, session manager, audit log, and data masking
- claw-sync: replaced inner claw-core copy with crates.io dependency
- claw-sync: implemented real gRPC client methods (was returning Default::default())
- All SDKs: added auto-provision entry point (clawdb() / ClawDB.autoProvision())
- MCP adapter: added --install-* commands for all major AI editors

## v0.1.1 — April 2026

### Changes
- Published claw-reflect-client Rust crate
- Fixed FTS5 desync on transaction rollback in claw-core
- Added tag index to claw-core (was using unindexed LIKE queries)
- Added pagination (cursor-based) to list operations

## v0.1.0 — March 2026

Initial release of all crates:
- claw-core, claw-vector, claw-guard, claw-sync, claw-branch
- clawdb aggregate runtime
- clawdb-server (gRPC + HTTP)
- clawdb-cli
- @clawdb/sdk (TypeScript)
- clawdb (Python)
- claw-cloud platform (alpha)
