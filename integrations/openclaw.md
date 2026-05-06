# OpenClaw

**Package:** `@clawdb/openclaw`

```bash
npm install @clawdb/openclaw @clawdb/sdk
```

The OpenClaw adapter attaches ClawDB to any OpenClaw agent with a single line, providing automatic memory injection, storage, and sync — all without any explicit tool calls in your agent code.

## Plugin API (recommended)

```typescript
import { OpenClawAgent } from 'openclaw'
import { ClawDBPlugin } from '@clawdb/openclaw'

const agent = new OpenClawAgent({
  plugins: [
    ClawDBPlugin({
      autoStore:      true,  // store every user + agent exchange
      autoSearch:     true,  // inject top memories before each turn
      topK:           3,     // memories injected per turn
      syncOnShutdown: true,  // push to cloud when agent exits
    })
  ],
})

// That's it. The agent now has a persistent database.
await agent.run('Hello!')
```

## HOC API

```typescript
import { withClawDB } from '@clawdb/openclaw'

const agent = withClawDB(new OpenClawAgent({ ... }))
```

## What happens automatically

Each agent turn:

1. **Before LLM call** — searches memory for the current user message, injects top matches into system context
2. **After LLM response** — stores the user message and agent response as memories tagged `auto-stored`
3. **On shutdown** — runs `db.sync.now()` to push to cloud

## Context format

Memory is injected as a system context block:

```
--- Relevant memories ---
[score: 0.94] User prefers async code patterns.
[score: 0.87] Last deployment was on May 1.
[score: 0.82] User's agent is named "Orion".
-------------------------
```
