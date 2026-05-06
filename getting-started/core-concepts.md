# Core Concepts

Understanding ClawDB's six primitives and how they compose.

---

## The six operations

Every ClawDB SDK exposes the same six namespaced operations regardless of language:

| Namespace | What it does |
|---|---|
| `db.memory` | Store and retrieve memories |
| `db.branch` | Fork state, simulate, merge |
| `db.sync` | Push/pull with the cloud hub |
| `db.reflect` | Auto-summarize and distill memories |
| `db.session` | Create and manage agent sessions |
| `db.health` | Check component status |

---

## Memory

A **memory** is the fundamental unit of storage in ClawDB. It is a piece of text (or structured data) attached to an agent, stored in SQLite, embedded into a vector index, and tagged with metadata.

```typescript
// Storing
const id = await db.memory.remember('Deploy every Friday after 3 PM UTC')

// Structured
const id = await db.memory.remember('Customer churned — budget cuts', {
  memoryType: 'fact',
  tags: ['crm', 'churn'],
  metadata: { customerId: 'cust_abc', confidence: 0.9 }
})
```

### Memory types

| Type | Use case |
|---|---|
| `message` | Conversation turns, user inputs |
| `fact` | Extracted facts and preferences |
| `task` | Action items and decisions |
| `summary` | Auto-generated session summaries |
| `semantic` | Rich associative knowledge |

### Search

ClawDB searches by **meaning**, not keywords. The underlying engine runs hybrid HNSW vector search plus FTS5 keyword search and blends the results:

```typescript
const hits = await db.memory.search('what are the user's deployment preferences?', {
  topK: 10,
  semantic: true,          // default: true
  filter: { tags: ['ops'] } // optional metadata filter
})
// hits[0].score → 0.97
// hits[0].content → "Deploy every Friday after 3 PM UTC"
```

---

## Sessions

A **session** is a scoped, time-limited token that an agent presents on every operation. Sessions carry:

- `agentId` — which agent is operating
- `role` — the agent's role (determines policy evaluation)
- `scopes` — a list of permitted operations (`memory:write`, `branch:fork`, etc.)
- `workspaceId` — which workspace the agent belongs to

```typescript
const session = await db.session.create({
  agentId: 'my-agent',
  role: 'assistant',
  scopes: ['memory:write', 'memory:read', 'sync:push']
})

// Pass the session to every operation
await db.memory.remember('content', { session })
```

Sessions expire after 1 hour by default (configurable). The Guard engine evaluates every operation against the session's role and scopes before executing.

---

## Branching

**Branching** lets an agent fork its current memory state into an isolated copy, experiment freely, and then either merge the results back or discard them. This turns reactive agents into deliberate planners.

```typescript
// Fork from trunk
const branchId = await db.branch.fork('pricing-experiment-A')

// Write to the branch — trunk is unaffected
await db.memory.remember('Proposal: increase tier price by 20%', { branch: branchId })

// Evaluate the outcome, then either:
await db.branch.merge(branchId, 'trunk')   // merge it in
// or
await db.branch.discard(branchId)          // throw it away
```

Branches use SQLite's Online Backup API for atomic snapshots. Every branch is BLAKE3-checksummed on creation and verified on open.

---

## Reflect (Auto-summarization)

**Reflect** is a background distillation pipeline that runs scheduled jobs to keep memory clean and useful over time. Without it, memory accumulates noise. With it, memory gets sharper.

The reflect pipeline has seven stages:

1. **Score** — assign importance scores to all memories
2. **Deduplicate** — collapse near-identical memories (cosine similarity > 0.95)
3. **Detect contradictions** — flag conflicting facts for review
4. **Summarize** — condense old sessions into compact summaries
5. **Extract facts** — pull structured preferences and facts from conversation text
6. **Decay** — reduce scores of stale memories exponentially over time
7. **Promote** — surface high-importance memories into the agent's profile

```typescript
// Trigger manually
const summary = await db.reflect()
// summary.factsExtracted  → 18
// summary.duplicatesRemoved → 3
// summary.memoriesDecayed   → 7
```

Reflect runs automatically on a schedule when connected to the cloud. For self-hosted, configure `REFLECT_BASE_URL` to point at your claw-reflect service.

---

## Sync

**Sync** replicates your agent's local memory to the cloud hub using an encrypted CRDT protocol. It is:

- **Offline-capable** — changes queue locally and drain when connectivity returns
- **Conflict-aware** — uses Hybrid Logical Clocks (HLC) for causal ordering
- **Auditable** — every sync round is chained with BLAKE3 hashes
- **Encrypted** — payloads use XSalsa20-Poly1305 + Ed25519 signatures

```typescript
const result = await db.sync.now()
// result.pushed    → 42
// result.pulled    → 7
// result.conflicts → 0
```

Sync is optional. You can use ClawDB entirely locally with no cloud connection.

---

## Auto-provisioning

The `clawdb()` shorthand function auto-detects the right connection mode in this priority order:

1. `CLAWDB_URL` environment variable → connect to that endpoint
2. `CLAWDB_API_KEY` environment variable → connect to `cloud.clawdb.dev`
3. `localhost:50050` reachable → connect to local server
4. Nothing found → download the server binary, start it, connect

This is why `const db = await clawdb()` works identically in local development, CI, and production — the same call, the right backend.

---

## The Guard engine

Every operation passes through the **Guard** engine before executing. Guard evaluates:

- The session's role and scopes
- The operation type (read, write, delete)
- The resource being accessed
- The computed risk score for the combination

If a policy denies the operation, it is rejected and logged to the audit trail — no exception, no bypass.

{% hint style="info" %}
For agents in production, always create sessions with the **minimum scopes** needed. An agent that only reads memories should use `scopes: ['memory:read']`, not `['*']`.
{% endhint %}
