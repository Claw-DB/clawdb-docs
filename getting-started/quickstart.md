# Quick Start

Get ClawDB running and your agent storing memories in under 60 seconds.

---

## Step 1 — Run init

Open a terminal in your project directory and run:

```bash
npx clawdb@latest init
```

ClawDB will:

1. Detect your environment (Node.js, Python, Rust, Go, or bare)
2. Auto-select a backend (SQLite locally, Postgres at scale)
3. Download the `clawdb-server` binary for your platform
4. Start the server on `localhost:50050`
5. Start a local embedding model (`all-MiniLM-L6-v2`)
6. Generate your first API key
7. Write connection config to `.clawdb.env`

Expected output:

```
┌─────────────────────────────────────────┐
│  ClawDB — zero-config agent database    │
└─────────────────────────────────────────┘
✓ Detected environment: Node.js project
✓ Auto-selected backend: SQLite (local)
↓ Downloading clawdb-server v0.1.2 ... done (3.2s)
✓ Server started on localhost:50050
✓ Embeddings: all-MiniLM-L6-v2 (local)
✓ Database initialised at ~/.clawdb/
✓ API key: sk_live_••••••••4f2a

Your agent now has a database.
```

---

## Step 2 — Install the SDK

{% tabs %}
{% tab title="TypeScript" %}
```bash
npm install @clawdb/sdk
# or
pnpm add @clawdb/sdk
```
{% endtab %}

{% tab title="Python" %}
```bash
pip install clawdb
```
{% endtab %}

{% tab title="Rust" %}
```toml
# Cargo.toml
[dependencies]
clawdb = "0.1"
```
{% endtab %}

{% tab title="Go" %}
```bash
go get github.com/Claw-DB/claw-sdk/sdks/go
```
{% endtab %}
{% endtabs %}

---

## Step 3 — Connect and use

{% tabs %}
{% tab title="TypeScript" %}
```typescript
import clawdb from '@clawdb/sdk'

// Auto-detects CLAWDB_URL or CLAWDB_API_KEY from env
const db = await clawdb()

// Store a memory
const id = await db.memory.remember('User prefers async code patterns')

// Search by meaning — not keywords
const hits = await db.memory.search('what does the user like?', { topK: 5 })

console.log(hits[0].content)  // "User prefers async code patterns"
console.log(hits[0].score)    // 0.97
```
{% endtab %}

{% tab title="Python" %}
```python
from clawdb import clawdb

db = await clawdb()

id = await db.memory.remember("User prefers async code patterns")

hits = await db.memory.search("what does the user like?", top_k=5)

print(hits[0].content)  # "User prefers async code patterns"
print(hits[0].score)    # 0.97
```
{% endtab %}

{% tab title="Rust" %}
```rust
use clawdb::prelude::*;

#[tokio::main]
async fn main() -> ClawDBResult<()> {
    let db = ClawDB::open_default().await?;
    let sx = db.session(agent_id, "assistant", vec!["memory:write".into()]).await?;

    db.remember(&sx, "User prefers async code patterns").await?;

    let hits = db.search(&sx, "what does the user like?").await?;
    println!("{}", hits[0].content);  // "User prefers async code patterns"
    println!("{}", hits[0].score);    // 0.97
    Ok(())
}
```
{% endtab %}
{% endtabs %}

---

## Step 4 — Connect Claude Desktop (optional)

If you use Claude Desktop, add ClawDB as an MCP server in one command:

```bash
npx @clawdb/mcp-adapter --install-claude
```

Restart Claude Desktop. Claude now has persistent memory — it can remember information across conversations using `clawdb_remember` and `clawdb_search` tools automatically.

---

## What's next?

<table data-card-size="medium" data-view="cards">
  <thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead>
  <tbody>
    <tr><td><strong>Core Concepts</strong></td><td>Understand memory, search, branches, reflect, sync</td><td><a href="core-concepts.md">core-concepts.md</a></td></tr>
    <tr><td><strong>Configuration</strong></td><td>All env vars and config file options</td><td><a href="configuration.md">configuration.md</a></td></tr>
    <tr><td><strong>Integrations</strong></td><td>LangChain, Vercel AI, OpenAI Agents, and more</td><td><a href="../integrations/mcp.md">../integrations/mcp.md</a></td></tr>
    <tr><td><strong>Cloud</strong></td><td>Sync across devices with clawdb.dev</td><td><a href="../cloud/overview.md">../cloud/overview.md</a></td></tr>
  </tbody>
</table>
