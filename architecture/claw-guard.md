# claw-guard — Policy Engine

**crates.io:** [crates.io/crates/claw-guard](https://crates.io/crates/claw-guard) · v0.1.2

Intent-aware access control. Evaluates task + resource + risk, not just identity.

## Decision flow

```
Validate session → Compute risk score → Evaluate policies → Allow | Deny | Mask
                                                                    ↓
                                                         Write audit log (always)
```

## Policy TOML format

```toml
[[policies]]
name     = "deny-finance-scheduling"
priority = 100
[[policies.rules]]
type   = "deny_if"
reason = "Finance data not accessible during scheduling"
[policies.rules.condition]
and = [{ task_matches = "scheduling" }, { resource_is = "finance_records" }]
```

## Audit log

Every `check_access()` writes an audit row — no exceptions. The log is append-only and queryable via `AuditReader::query()`.

See [Audit Log](../security/audit-log.md) for querying details.
