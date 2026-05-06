# Go SDK

**Module:** `github.com/Claw-DB/claw-sdk/sdks/go`  
**Requires:** Go 1.21+

---

## Installation

```bash
go get github.com/Claw-DB/claw-sdk/sdks/go
```

---

## Quick start

```go
package main

import (
    "context"
    "fmt"
    "os"

    clawdb "github.com/Claw-DB/claw-sdk/sdks/go"
)

func main() {
    ctx := context.Background()

    db, err := clawdb.New(clawdb.Options{
        AgentID: "my-agent",
        // Reads CLAWDB_URL or CLAWDB_API_KEY from env automatically
    })
    if err != nil {
        panic(err)
    }
    defer db.Close()

    id, _ := db.Memory.Remember(ctx, "User prefers dark mode", nil)
    fmt.Println("Stored:", id)

    hits, _ := db.Memory.Search(ctx, "UI preferences", &clawdb.SearchOptions{TopK: 5})
    fmt.Println("Score:", hits[0].Score)
}
```

---

## Full API reference

### Memory

```go
// Remember
id, err := db.Memory.Remember(ctx, content string, opts *MemoryOptions) (string, error)

// Search
hits, err := db.Memory.Search(ctx, query string, opts *SearchOptions) ([]SearchHit, error)

// Recall
memories, err := db.Memory.Recall(ctx, ids []string) ([]SearchHit, error)

// List
page, err := db.Memory.List(ctx, opts *ListOptions) (*MemoryPage, error)

// Delete
err := db.Memory.Delete(ctx, id string) error
```

### Branch

```go
branchID, err := db.Branch.Fork(ctx, name string) (string, error)
diff, err     := db.Branch.Diff(ctx, source, target string) (*BranchDiff, error)
result, err   := db.Branch.Merge(ctx, source, target string, opts *MergeOptions) (*MergeResult, error)
err           := db.Branch.Discard(ctx, branchID string) error
branches, err := db.Branch.List(ctx, opts *ListBranchOptions) ([]BranchInfo, error)
```

### Sync and Reflect

```go
result, err  := db.Sync.Now(ctx) (*SyncResult, error)
status, err  := db.Sync.Status(ctx) (*SyncStatus, error)

summary, err := db.Reflect.Run(ctx, opts *ReflectOptions) (*ReflectSummary, error)
```

### Health

```go
status, err := db.Health.Check(ctx) (*HealthStatus, error)
```

---

## Types

```go
type SearchHit struct {
    ID         string
    Content    string
    Score      float64
    MemoryType string
    Tags       []string
    Metadata   map[string]interface{}
    CreatedAt  time.Time
}

type MemoryOptions struct {
    MemoryType string
    Tags       []string
    Metadata   map[string]interface{}
}

type SearchOptions struct {
    TopK     int
    Semantic bool
    Filter   map[string]interface{}
}
```

---

## Error handling

```go
import "github.com/Claw-DB/claw-sdk/sdks/go/errors"

id, err := db.Memory.Remember(ctx, "content", nil)
if err != nil {
    var authErr *errors.AuthError
    var rateErr *errors.RateLimitError
    switch {
    case errors.As(err, &authErr):
        fmt.Println("Check CLAWDB_API_KEY")
    case errors.As(err, &rateErr):
        time.Sleep(time.Duration(rateErr.RetryAfterMs) * time.Millisecond)
    default:
        return err
    }
}
```

---

## Context and cancellation

All methods respect `context.Context` for timeouts and cancellation:

```go
ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
defer cancel()

hits, err := db.Memory.Search(ctx, "query", nil)
// Automatically cancelled after 5s if no response
```
