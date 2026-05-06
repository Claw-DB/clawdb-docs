# Audit Log

Every access control decision is recorded in the audit log. No operation — allowed or denied — skips the log.

## What is logged

| Field | Description |
|---|---|
| `ts` | Timestamp of the decision |
| `session_id` | Session that made the request |
| `agent_id` | Agent UUID |
| `workspace_id` | Workspace |
| `action` | Operation type (write, read, delete, sync, etc.) |
| `resource` | Resource being accessed |
| `decision` | Allow, Deny, or Mask |
| `reason` | Policy name and rule that triggered (for Deny/Mask) |
| `risk_score` | Computed risk score at decision time |

## Querying the audit log

```bash
# CLI
clawdb audit --decision deny --limit 50 --output json

# Export CSV
clawdb audit --from 2026-05-01 --to 2026-05-31 --export audit-may.csv
```

## Cloud audit log retention

| Plan | Retention |
|---|---|
| Starter | 7 days |
| Pro | 90 days |
| Enterprise | 1 year |

## Audit log integrity

The audit log is BLAKE3-chained: each row includes the hash of the previous row, making tampering detectable.

```bash
clawdb audit verify-chain
# ✓ Audit chain verified — 12,841 entries intact
```
