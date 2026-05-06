# Google GenAI

**Package:** `@clawdb/google-genai`

```bash
npm install @clawdb/google-genai @clawdb/sdk @google/generative-ai
```

## FunctionDeclarations

```typescript
import { GoogleGenerativeAI } from '@google/generative-ai'
import clawdb from '@clawdb/sdk'
import { clawdbTools, handleClawDBFunctionCall } from '@clawdb/google-genai'

const genAI = new GoogleGenerativeAI(process.env.GOOGLE_API_KEY)
const db    = await clawdb()

const model = genAI.getGenerativeModel({
  model: 'gemini-2.0-flash',
  tools: [{ functionDeclarations: clawdbTools(db) }],
})

const result = await model.generateContent('What do you remember about me?')

// Handle function calls
for (const call of result.response.functionCalls() ?? []) {
  const response = await handleClawDBFunctionCall(db, call)
  // Continue the conversation with the response
}
```

## Auto-memory wrapper

```typescript
import { withClawDBMemory } from '@clawdb/google-genai'

const model = withClawDBMemory(
  genAI.getGenerativeModel({ model: 'gemini-2.0-flash' }),
  db,
)
```
