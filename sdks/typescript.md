# TypeScript / Node.js SDK

**Package:** `@clawdb/sdk`  
**Registry:** [npmjs.com/package/@clawdb/sdk](https://www.npmjs.com/package/@clawdb/sdk)  
**Requires:** Node.js ≥ 18

---

## Installation

```bash
npm install @clawdb/sdk
# or
pnpm add @clawdb/sdk
# or
yarn add @clawdb/sdk
```

---

## Auto-provisioned entry point

The simplest way to connect. Reads `CLAWDB_API_KEY` or `CLAWDB_URL` from your environment and provisions automatically:

```typescript
import clawdb from '@clawdb/sdk'

const db = await clawdb()
```

For explicit connection:

```typescript
import { ClawDB } from '@clawdb/sdk'

const db = await ClawDB.connect({
  endpoint:    'http://localhost:50050',
  apiKey:      process.env.CLAWDB_API_KEY,
  workspaceId: process.env.CLAWDB_WORKSPACE_ID,
})
```

---

## Memory

### `db.memory.remember(content, options?)`

Store a memory. Returns the memory ID.

```typescript
// Simple
const id = await db.memory.remember('User prefers dark mode')

// Typed with metadata
const id = await db.memory.remember('Deploy budget approved: $50k', {
  memoryType: 'fact',
  tags:       ['budget', 'ops'],
  metadata:   { amount: 50000, currency: 'USD' },
})
```

**Options:**

| Field | Type | Default | Description |
|---|---|---|---|
| `memoryType` | `string` | `'message'` | One of `message`, `fact`, `task`, `summary`, `semantic` |
| `tags` | `string[]` | `[]` | Searchable string tags |
| `metadata` | `Record<string, unknown>` | `{}` | Arbitrary JSON metadata |
| `session` | `ClawDBSession` | auto | Override the default session |
| `signal` | `AbortSignal` | — | Cancellation signal |

---

### `db.memory.search(query, options?)`

Search memories by meaning. Returns an array of `SearchHit` objects sorted by score.

```typescript
const hits = await db.memory.search('what are the deployment policies?', {
  topK:     10,
  semantic: true,
  filter:   { tags: ['ops'] },
})

hits.forEach(h => {
  console.log(h.content, h.score, h.tags)
})
```

**Options:**

| Field | Type | Default | Description |
|---|---|---|---|
| `topK` | `number` | `10` | Maximum number of results |
| `semantic` | `boolean` | `true` | Use HNSW vector search. Set `false` for keyword-only FTS5 |
| `filter` | `object` | — | Metadata filter (see [Filtering](#filtering)) |
| `signal` | `AbortSignal` | — | Cancellation signal |

**`SearchHit` type:**

```typescript
interface SearchHit {
  id:          string
  content:     string
  score:       number        // 0.0 – 1.0
  memoryType:  string
  tags:        string[]
  metadata:    Record<string, unknown>
  createdAt:   Date
}
```

---

### `db.memory.recall(ids)`

Retrieve specific memories by ID.

```typescript
const memories = await db.memory.recall(['mem_abc123', 'mem_def456'])
```

---

### `db.memory.list(options?)`

Paginate through all memories with an optional type or tag filter.

```typescript
const { hits, nextCursor } = await db.memory.list({
  type:   'fact',
  limit:  50,
  cursor: previousCursor,
})
```

---

### `db.memory.delete(id)`

Delete a memory by ID.

```typescript
await db.memory.delete('mem_abc123')
```

---

## Branching

```typescript
// Fork from trunk
const branchId = await db.branch.fork('experiment-A')

// Write to the branch
await db.memory.remember('Hypothesis: increase price 20%', { branch: branchId })

// See what changed
const diff = await db.branch.diff(branchId, 'trunk')
console.log(diff.added.length)    // 1
console.log(diff.modified.length) // 0

// Merge if it worked
await db.branch.merge(branchId, 'trunk', { strategy: 'last-write' })

// Or discard
await db.branch.discard(branchId)
```

---

## Sync

```typescript
const result = await db.sync.now()
// { pushed: 42, pulled: 7, conflicts: 0, durationMs: 312 }

const status = await db.sync.status()
// { connected: true, queueDepth: 0, lastSyncAt: Date }
```

---

## Reflect

```typescript
const summary = await db.reflect({ dryRun: false })
// { factsExtracted: 18, duplicatesRemoved: 3, memoriesDecayed: 7 }
```

---

## Filtering

The `filter` parameter in `search()` and `list()` accepts:

```typescript
// By tag
{ tags: ['ops', 'deploy'] }

// By metadata field
{ metadata: { customerId: 'cust_abc' } }

// By type
{ type: 'fact' }

// Combined
{ tags: ['ops'], type: 'fact' }
```

---

## Error handling

All SDK errors extend `ClawDBError`:

```typescript
import {
  ClawDBError,
  ClawDBAuthError,
  ClawDBNotFoundError,
  ClawDBRateLimitError,
  ClawDBUnavailableError,
  ClawDBTimeoutError,
} from '@clawdb/sdk'

try {
  await db.memory.remember('...')
} catch (err) {
  if (err instanceof ClawDBRateLimitError) {
    await sleep(err.retryAfterMs)
  } else if (err instanceof ClawDBAuthError) {
    // Check your API key
  } else {
    throw err
  }
}
```

---

## Retry behaviour

The SDK retries automatically on `UNAVAILABLE`, `DEADLINE_EXCEEDED`, and `INTERNAL` gRPC status codes with exponential backoff:

| Attempt | Delay |
|---|---|
| 1 | 200ms + jitter |
| 2 | 400ms + jitter |
| 3 | 800ms + jitter |

`ClawDBAuthError` (401) and `ClawDBNotFoundError` (404) are **never** retried.

---

## TypeScript types

All types are exported from the package root:

```typescript
import type {
  SearchHit,
  MemoryOptions,
  SearchOptions,
  BranchInfo,
  MergeResult,
  SyncStatus,
  ReflectSummary,
  ClawDBSession,
  HealthStatus,
} from '@clawdb/sdk'
```

---

## Framework adapters

For framework-specific integrations, install the matching adapter:

| Framework | Package |
|---|---|
| LangChain | `@clawdb/langchain` |
| Vercel AI SDK | `@clawdb/vercel-ai` |
| OpenAI Agents | `@clawdb/openai-agents` |
| Anthropic SDK | `@clawdb/anthropic` |
| Google GenAI | `@clawdb/google-genai` |
| OpenClaw | `@clawdb/openclaw` |

See the [Integrations](../integrations/mcp.md) section for details.
