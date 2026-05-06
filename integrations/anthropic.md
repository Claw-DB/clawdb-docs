# Anthropic SDK

**Package:** `@clawdb/anthropic`

```bash
npm install @clawdb/anthropic @clawdb/sdk @anthropic-ai/sdk
```

## Native tool format

```typescript
import Anthropic from '@anthropic-ai/sdk'
import clawdb from '@clawdb/sdk'
import { clawdbTools, handleClawDBToolCall } from '@clawdb/anthropic'

const anthropic = new Anthropic()
const db        = await clawdb()

let messages = [{ role: 'user', content: 'What do you remember about me?' }]

const response = await anthropic.messages.create({
  model:   'claude-sonnet-4-20250514',
  max_tokens: 1024,
  tools:   clawdbTools(db),
  messages,
})

// Handle tool use blocks
for (const block of response.content) {
  if (block.type === 'tool_use') {
    const result = await handleClawDBToolCall(db, block)
    messages.push({ role: 'user', content: [result] })
  }
}
```

## Auto-memory wrapper

```typescript
import { withClawDBMemory } from '@clawdb/anthropic'

const client = withClawDBMemory(new Anthropic(), db)

// Pre-loads relevant memory, stores turns automatically
const response = await client.messages.create({
  model: 'claude-sonnet-4-20250514',
  max_tokens: 1024,
  messages: [{ role: 'user', content: 'Hello!' }],
})
```
