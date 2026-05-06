# OpenAI Agents

**Package:** `@clawdb/openai-agents`

```bash
npm install @clawdb/openai-agents @clawdb/sdk @openai/agents
```

## `createClawDBAgentTools`

```typescript
import { Agent, run } from '@openai/agents'
import clawdb from '@clawdb/sdk'
import { createClawDBAgentTools } from '@clawdb/openai-agents'

const db    = await clawdb()
const tools = createClawDBAgentTools(db, { enableBranching: true })

const agent = new Agent({
  name:         'My Agent',
  instructions: 'You are a helpful assistant with persistent memory.',
  tools,
})

await run(agent, 'What do you remember about the user?')
```

Available tools: `remember_memory`, `search_memory`, `recall_memory`, `fork_branch`, `merge_branch`

## `withClawDBMemory`

Wraps any Agent with automatic memory injection and storage:

```typescript
import { withClawDBMemory } from '@clawdb/openai-agents'

const agent = withClawDBMemory(
  new Agent({ name: 'My Agent', instructions: '...' }),
  db,
  {
    topK:        3,    // memories injected per run
    autoStore:   true, // store user + assistant turns automatically
  }
)

// Memory is injected before each run and stored after
await run(agent, 'Hello!')
```
