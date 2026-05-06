# Rust SDK

ClawDB ships two Rust crates for different use cases:

| Crate | When to use |
|---|---|
| `clawdb` | Embedded runtime — the full engine runs in-process. Best for native applications and edge devices. |
| `clawdb-client` | Lightweight gRPC/HTTP client — talks to a running `clawdb-server`. Best for services that share a central server. |

---

## `clawdb` — embedded runtime

**Registry:** [crates.io/crates/clawdb](https://crates.io/crates/clawdb)

```toml
[dependencies]
clawdb  = "0.1"
tokio   = { version = "1", features = ["full"] }
```

```rust
use clawdb::prelude::*;

#[tokio::main]
async fn main() -> ClawDBResult<()> {
    // Open with defaults (reads CLAWDB_DATA_DIR, CLAW_GUARD_JWT_SECRET)
    let db = ClawDB::open_default().await?;

    // Create a session for the agent
    let session = db.session(
        agent_id,
        "assistant",
        vec!["memory:write".into(), "memory:read".into()],
    ).await?;

    // Store a memory
    db.remember(&session, "User prefers async Rust patterns").await?;

    // Search by meaning
    let hits = db.search(&session, "what does the user like?").await?;
    println!("{} — score: {}", hits[0].content, hits[0].score);

    // Branch and merge
    let branch = db.branch(&session, "experiment-A").await?;
    db.merge(&session, branch, Uuid::nil(), MergeStrategy::LastWrite).await?;

    // Distill
    let summary = db.reflect(&session).await?;
    println!("Facts extracted: {}", summary.facts_extracted);

    // Sync to cloud
    let sync = db.sync(&session).await?;
    println!("Pushed: {}, Pulled: {}", sync.pushed, sync.pulled);

    Ok(())
}
```

### Re-exported types

The `clawdb::prelude` re-exports all types you need so you never import component crates directly:

```rust
pub use clawdb::prelude::{
    ClawDB, ClawDBConfig, ClawDBSession, ClawDBResult, ClawDBError,
    MemoryRecord, MemoryType, SearchHit, MergeStrategy, BranchDiff,
    SyncSummary, ReflectSummary, HealthStatus,
};
```

---

## `clawdb-client` — lightweight HTTP/gRPC client

**Registry:** [crates.io/crates/clawdb-client](https://crates.io/crates/clawdb-client)

```toml
[dependencies]
clawdb-client = "0.1"
tokio         = { version = "1", features = ["full"] }
```

```rust
use clawdb_client::ClawDBClient;

#[tokio::main]
async fn main() -> clawdb_client::Result<()> {
    // Auto-provision: reads CLAWDB_URL or CLAWDB_API_KEY
    let db = ClawDBClient::auto_provision().await?;

    // Or explicit builder
    let db = ClawDBClient::builder()
        .endpoint("http://localhost:50050")
        .api_key(std::env::var("CLAWDB_API_KEY")?)
        .build()
        .await?;

    // Builder pattern for operations
    let id = db.memory().remember("content").await?;
    let hits = db.memory().search("query").top_k(5).call().await?;

    Ok(())
}
```

---

## Error handling

```rust
use clawdb::ClawDBError;

match db.remember(&session, "content").await {
    Ok(id)  => println!("Stored: {}", id),
    Err(ClawDBError::PermissionDenied(reason)) => {
        eprintln!("Guard denied: {}", reason);
    }
    Err(ClawDBError::SessionInvalid) => {
        // Refresh session
    }
    Err(e) => return Err(e),
}
```

---

## Feature flags

```toml
[dependencies]
clawdb = { version = "0.1", features = ["encryption"] }
```

| Feature | Description |
|---|---|
| `encryption` | Enables SQLCipher encryption at rest (requires SQLCipher build) |

---

# Go SDK

**Module:** `github.com/Claw-DB/claw-sdk/sdks/go`

```bash
go get github.com/Claw-DB/claw-sdk/sdks/go
```

---

## Usage

```go
package main

import (
    "context"
    "fmt"
    clawdb "github.com/Claw-DB/claw-sdk/sdks/go"
)

func main() {
    ctx := context.Background()

    // Auto-provision from CLAWDB_URL or CLAWDB_API_KEY
    db, err := clawdb.New(clawdb.Options{
        AgentID: "my-agent",
    })
    if err != nil {
        panic(err)
    }
    defer db.Close()

    // Store
    id, err := db.Memory.Remember(ctx, "User prefers Go concurrency", nil)
    if err != nil {
        panic(err)
    }
    fmt.Println("Stored:", id)

    // Search
    hits, err := db.Memory.Search(ctx, "language preferences", &clawdb.SearchOptions{
        TopK:     5,
        Semantic: true,
    })
    for _, h := range hits {
        fmt.Printf("%s (score: %.2f)\n", h.Content, h.Score)
    }

    // Sync
    result, err := db.Sync.Now(ctx)
    fmt.Printf("Pushed: %d, Pulled: %d\n", result.Pushed, result.Pulled)
}
```

---

## Options

```go
type Options struct {
    Endpoint    string  // overrides auto-provision
    APIKey      string  // overrides CLAWDB_API_KEY env var
    AgentID     string  // required
    WorkspaceID string  // optional
    TimeoutMs   int     // default: 10000
    MaxRetries  int     // default: 3
}
```

All methods accept a `context.Context` as the first argument, supporting cancellation and timeouts natively.
