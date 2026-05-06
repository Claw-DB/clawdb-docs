# Python SDK

**Package:** `clawdb`  
**Registry:** [pypi.org/project/clawdb](https://pypi.org/project/clawdb/)  
**Requires:** Python ≥ 3.10

---

## Installation

```bash
pip install clawdb
```

---

## Two clients: sync and async

ClawDB provides both a synchronous and an asynchronous client. The `clawdb()` shorthand auto-detects the calling context:

```python
from clawdb import clawdb

# In an async context (FastAPI, asyncio, etc.)
db = await clawdb()

# In a sync context (scripts, Jupyter, Flask)
db = clawdb()   # no await
```

---

## Async client (`AsyncClawDB`)

```python
from clawdb import AsyncClawDB

async with AsyncClawDB(
    endpoint="http://localhost:50050",
    api_key=os.environ["CLAWDB_API_KEY"],
) as db:
    id = await db.memory.remember("User prefers Python")
    hits = await db.memory.search("language preferences", top_k=5)
```

---

## Sync client (`ClawDB`)

```python
from clawdb import ClawDB

with ClawDB(endpoint="http://localhost:50050") as db:
    id = db.memory.remember("User prefers Python")
    hits = db.memory.search("language preferences", top_k=5)
```

The sync client runs async methods in a dedicated thread-safe event loop, so it works in Jupyter notebooks and standard scripts without any async overhead.

---

## Memory

### `db.memory.remember(content, **kwargs)`

```python
# Simple
id = await db.memory.remember("Deploy every Friday after 3 PM UTC")

# With options
id = await db.memory.remember(
    "Customer churned — budget cuts",
    memory_type="fact",
    tags=["crm", "churn"],
    metadata={"customer_id": "cust_abc", "confidence": 0.9},
)
```

### `db.memory.search(query, **kwargs)`

```python
hits = await db.memory.search(
    "what are the deployment policies?",
    top_k=10,
    semantic=True,
    filter={"tags": ["ops"]},
)

for hit in hits:
    print(hit.content, hit.score)
```

**`SearchHit` dataclass:**

```python
@dataclass
class SearchHit:
    id:          str
    content:     str
    score:       float        # 0.0 – 1.0
    memory_type: str
    tags:        list[str]
    metadata:    dict[str, Any]
    created_at:  datetime
```

### `db.memory.recall(ids)`

```python
memories = await db.memory.recall(["mem_abc123", "mem_def456"])
```

### `db.memory.list(type=None, limit=50, cursor=None)`

```python
hits, next_cursor = await db.memory.list(type="fact", limit=50)
```

### `db.memory.delete(id)`

```python
await db.memory.delete("mem_abc123")
```

---

## Branching

```python
branch_id = await db.branch.fork("experiment-A")

await db.memory.remember("Hypothesis: increase price 20%", branch=branch_id)

diff = await db.branch.diff(branch_id, "trunk")
print(diff.added)    # list of new memories

await db.branch.merge(branch_id, "trunk", strategy="last_write")
# or
await db.branch.discard(branch_id)
```

---

## Sync and reflect

```python
result = await db.sync.now()
# SyncResult(pushed=42, pulled=7, conflicts=0, duration_ms=312)

summary = await db.reflect(dry_run=False)
# ReflectSummary(facts_extracted=18, duplicates_removed=3, memories_decayed=7)
```

---

## Error handling

```python
from clawdb.exceptions import (
    ClawDBError,
    ClawDBAuthError,
    ClawDBNotFoundError,
    ClawDBRateLimitError,
    ClawDBUnavailableError,
    ClawDBTimeoutError,
)

try:
    await db.memory.remember("...")
except ClawDBRateLimitError as e:
    await asyncio.sleep(e.retry_after_seconds)
except ClawDBAuthError:
    # Check CLAWDB_API_KEY
    raise
```

---

## Type stubs

The package ships with `.pyi` stub files for full mypy and Pyright support. No extra packages needed.

```bash
mypy your_agent.py  # fully typed
```

---

## LangChain integration

```python
from clawdb.langchain import ClawDBRetriever, ClawDBChatMessageHistory

retriever = ClawDBRetriever(client=db, top_k=10)
history   = ClawDBChatMessageHistory(client=db, session_id="session_abc")
```

See [LangChain integration](../integrations/langchain.md) for the full guide.
