# Vercel AI SDK

**Package:** `@clawdb/vercel-ai`

```bash
npm install @clawdb/vercel-ai @clawdb/sdk ai
```

## Tools for `generateText` / `streamText`

```typescript
import { generateText } from 'ai'
import { openai } from '@ai-sdk/openai'
import clawdb from '@clawdb/sdk'
import { clawdbTools } from '@clawdb/vercel-ai'

const db = await clawdb()

const { text } = await generateText({
  model:  openai('gpt-4o'),
  tools:  clawdbTools(db),
  prompt: 'What do you remember about my preferences?',
})
```

## Automatic middleware

Wraps any model to inject memory context before calls and store turns after:

```typescript
import { wrapLanguageModel } from 'ai'
import { clawdbMiddleware } from '@clawdb/vercel-ai'

const model = wrapLanguageModel({
  model:      openai('gpt-4o'),
  middleware: clawdbMiddleware(db),
})

// Memory is injected and stored automatically — no tool calls needed
const { text } = await generateText({ model, prompt: 'Hello!' })
```

## React hook

```typescript
import { useClawDB } from '@clawdb/vercel-ai/react'

export default function Page() {
  const { db, loading, error } = useClawDB()

  if (loading) return <div>Connecting...</div>
  // db is ready to use
}
```
