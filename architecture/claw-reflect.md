# claw-reflect — Summarization

The autonomous 7-stage memory distillation pipeline. Runs as a Python service with a thin Rust client (`claw-reflect-client`).

## Pipeline stages

| Stage | What it does |
|---|---|
| Score | Recency × 0.3 + access_count × 0.4 + base × 0.3 |
| Deduplicate | Collapse memories with cosine similarity > 0.95 |
| Detect contradictions | Flag conflicting facts (LLM-powered, Anthropic API) |
| Summarize | Condense old sessions to 3–5 bullets (LLM-powered) |
| Extract facts | Pull preferences and facts from conversation text (LLM-powered) |
| Decay | `score × exp(-0.05 × days_since_access)` |
| Promote | Elevate high-importance memories to agent profile |

## LLM call safety

- All prompts strip null bytes and truncate to 8,192 chars
- Retried up to 3× with exponential backoff
- Jobs are idempotent — running twice produces the same result
- API keys never appear in prompts or logs
