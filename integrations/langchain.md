# LangChain

**Package:** `@clawdb/langchain` (TypeScript) · `clawdb[langchain]` (Python)

---

## Installation

{% tabs %}
{% tab title="TypeScript" %}
```bash
npm install @clawdb/langchain @clawdb/sdk @langchain/core
```
{% endtab %}

{% tab title="Python" %}
```bash
pip install "clawdb[langchain]"
# installs clawdb + langchain-core
```
{% endtab %}
{% endtabs %}

---

## `ClawDBRetriever`

A drop-in LangChain retriever that searches ClawDB memories by semantic similarity.

{% tabs %}
{% tab title="TypeScript" %}
```typescript
import clawdb from '@clawdb/sdk'
import { ClawDBRetriever } from '@clawdb/langchain'
import { ChatAnthropic } from '@langchain/anthropic'
import { createStuffDocumentsChain } from 'langchain/chains/combine_documents'

const db = await clawdb()

const retriever = new ClawDBRetriever({
  client: db,
  topK:   10,
  filter: { tags: ['project-alpha'] },
})

// Use in any LangChain RAG chain
const llm   = new ChatAnthropic({ model: 'claude-sonnet-4-20250514' })
const chain = await createStuffDocumentsChain({ llm, prompt })

const result = await chain.invoke({
  question: "What are the latest decisions on Project Alpha?",
  context: await retriever.invoke("Project Alpha decisions"),
})
```
{% endtab %}

{% tab title="Python" %}
```python
from clawdb import clawdb
from clawdb.langchain import ClawDBRetriever
from langchain_anthropic import ChatAnthropic

db = await clawdb()

retriever = ClawDBRetriever(client=db, top_k=10)

llm   = ChatAnthropic(model="claude-sonnet-4-20250514")
chain = create_stuff_documents_chain(llm=llm, prompt=prompt)

result = await chain.ainvoke({
    "question": "What are the latest decisions on Project Alpha?",
    "context": await retriever.ainvoke("Project Alpha decisions"),
})
```
{% endtab %}
{% endtabs %}

---

## `ClawDBChatMessageHistory`

Persistent chat history stored in ClawDB, replacing in-memory `ChatMessageHistory`.

{% tabs %}
{% tab title="TypeScript" %}
```typescript
import { ClawDBChatMessageHistory } from '@clawdb/langchain'
import { RunnableWithMessageHistory } from '@langchain/core/runnables'

const history = new ClawDBChatMessageHistory({
  client:    db,
  sessionId: 'user-session-abc',
})

const chainWithHistory = new RunnableWithMessageHistory({
  runnable:            chain,
  getMessageHistory:   () => history,
  inputMessagesKey:    'input',
  historyMessagesKey:  'chat_history',
})
```
{% endtab %}

{% tab title="Python" %}
```python
from clawdb.langchain import ClawDBChatMessageHistory
from langchain_core.runnables.history import RunnableWithMessageHistory

history = ClawDBChatMessageHistory(client=db, session_id="user-session-abc")

chain_with_history = RunnableWithMessageHistory(
    chain,
    get_session_history=lambda sid: ClawDBChatMessageHistory(client=db, session_id=sid),
    input_messages_key="input",
    history_messages_key="chat_history",
)
```
{% endtab %}
{% endtabs %}

---

## `createClawDBTools`

Expose ClawDB operations as LangChain tools the agent can call directly.

{% tabs %}
{% tab title="TypeScript" %}
```typescript
import { createClawDBTools } from '@clawdb/langchain'
import { createReactAgent } from '@langchain/langgraph/prebuilt'

const tools = createClawDBTools(db)
// Tools: remember_memory, search_memory, recall_memory

const agent = createReactAgent({ llm, tools })
```
{% endtab %}

{% tab title="Python" %}
```python
from clawdb.langchain import create_clawdb_tools

tools = create_clawdb_tools(db)
# Tools: remember_memory, search_memory, recall_memory

agent = create_react_agent(llm, tools)
```
{% endtab %}
{% endtabs %}
