# ClawDB Documentation

> **The zero-config database for AI agents.** Give any agent production-ready storage, semantic search, branching, sync, and auto-summarization — in one line of code.

---

## What is ClawDB?

ClawDB is a **unified cognitive database runtime** built specifically for autonomous AI agents. Instead of wiring together five separate tools (a storage database, a vector database, an embedding pipeline, a memory layer, and a sync engine), ClawDB ships all of it as a single runtime you install in seconds.

```bash
npx clawdb@latest init
```

That single command detects your environment, selects the right backend, starts the server, and hands you a working database. Your agent can start remembering, searching, branching, and syncing immediately.

---

## Quick Navigation

<table data-view="cards">
  <thead>
    <tr>
      <th></th>
      <th></th>
      <th data-hidden data-card-target data-type="content-ref"></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>🚀 Quick Start</strong></td>
      <td>Up and running in under 60 seconds</td>
      <td><a href="getting-started/quickstart.md">getting-started/quickstart.md</a></td>
    </tr>
    <tr>
      <td><strong>📦 SDKs</strong></td>
      <td>TypeScript, Python, Rust, Go</td>
      <td><a href="sdks/typescript.md">sdks/typescript.md</a></td>
    </tr>
    <tr>
      <td><strong>🔌 Integrations</strong></td>
      <td>Claude, LangChain, OpenAI, Vercel AI</td>
      <td><a href="integrations/mcp.md">integrations/mcp.md</a></td>
    </tr>
    <tr>
      <td><strong>☁️ Cloud</strong></td>
      <td>Managed hosting at clawdb.dev</td>
      <td><a href="cloud/overview.md">cloud/overview.md</a></td>
    </tr>
    <tr>
      <td><strong>🏗️ Architecture</strong></td>
      <td>How the seven engines work together</td>
      <td><a href="architecture/overview.md">architecture/overview.md</a></td>
    </tr>
    <tr>
      <td><strong>🔒 Security</strong></td>
      <td>Policy engine, audit log, encryption</td>
      <td><a href="security/overview.md">security/overview.md</a></td>
    </tr>
  </tbody>
</table>

---

## The One-Line Promise

Most agent databases require you to:

- Pick a storage backend
- Wire up a vector database separately
- Manage an embedding pipeline
- Write a custom memory layer
- Think about sync and conflict resolution

ClawDB collapses all of that into one import:

{% tabs %}
{% tab title="TypeScript" %}
```typescript
import clawdb from '@clawdb/sdk'

const db = await clawdb()  // auto-provisions everything

await db.memory.remember('user prefers dark mode')
const hits = await db.memory.search('user preferences')
```
{% endtab %}

{% tab title="Python" %}
```python
from clawdb import clawdb

db = await clawdb()  # auto-provisions everything

await db.memory.remember("user prefers dark mode")
hits = await db.memory.search("user preferences")
```
{% endtab %}

{% tab title="Rust" %}
```rust
use clawdb::prelude::*;

let db = ClawDB::open_default().await?;
let sx = db.session(agent_id, "assistant", scopes).await?;

db.remember(&sx, "user prefers dark mode").await?;
let hits = db.search(&sx, "user preferences").await?;
```
{% endtab %}

{% tab title="Go" %}
```go
import clawdb "github.com/Claw-DB/claw-sdk/sdks/go"

db, _ := clawdb.New(clawdb.Options{AgentID: "my-agent"})
defer db.Close()

db.Memory.Remember(ctx, "user prefers dark mode", nil)
hits, _ := db.Memory.Search(ctx, "user preferences", nil)
```
{% endtab %}
{% endtabs %}

---

## Version

Current stable release: **v0.1.2**

| Package | Registry | Version |
|---|---|---|
| `clawdb` | crates.io | [![crates.io](https://img.shields.io/crates/v/clawdb)](https://crates.io/crates/clawdb) |
| `@clawdb/sdk` | npm | [![npm](https://img.shields.io/npm/v/@clawdb/sdk)](https://npmjs.com/package/@clawdb/sdk) |
| `clawdb` | PyPI | [![PyPI](https://img.shields.io/pypi/v/clawdb)](https://pypi.org/project/clawdb/) |

{% hint style="info" %}
ClawDB is Apache-2.0 licensed and fully open source. The cloud platform at [clawdb.dev](https://clawdb.dev) is the optional managed layer.
{% endhint %}
