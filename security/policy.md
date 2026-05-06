# Policy Rules

Policy rules control what agents can do based on their task, role, resource, and computed risk score.

## Writing policies

Policies are TOML files loaded from `CLAW_GUARD_POLICY_DIR`:

```toml
[[policies]]
name        = "my-policy"
description = "Example policy"
priority    = 100
enabled     = true

[[policies.rules]]
type   = "deny_if"
reason = "Finance data not accessible during scheduling tasks"

[policies.rules.condition]
and = [
  { task_matches = "scheduling" },
  { resource_is  = "finance_records" },
]

[[policies.rules]]
type          = "mask_field"
field_pattern = "email"
mask_type     = "EmailMask"
```

## Condition types

| Condition | Description |
|---|---|
| `task_matches` | Agent's declared task contains this string |
| `role_is` | Session role equals this string |
| `scope_contains` | Session scopes include this string |
| `risk_above` | Computed risk score > threshold (0.0–1.0) |
| `resource_is` | Resource name equals this string |
| `workspace_is` | Workspace ID equals this UUID |
| `and` | Both sub-conditions must be true |
| `or` | Either sub-condition must be true |
| `not` | Sub-condition must be false |

## Risk score

ClawDB computes a risk score for each operation:

| Factor | Score addition |
|---|---|
| Write action | +0.3 |
| Delete action | +0.5 |
| Resource in `CLAW_GUARD_SENSITIVE_RESOURCES` | +0.3 |
| Outside business hours | +0.1 |

Scores are clamped to `[0.0, 1.0]`.

## Policy priority

Policies are evaluated in descending priority order. The first matching rule that produces a non-default decision wins. If no rule matches, the default decision is **Deny**.

## Testing policies

```bash
clawdb policy test \
  --agent-id agent-prod \
  --role assistant \
  --task scheduling \
  --resource finance_records
# Result: DENY — "Finance data not accessible during scheduling tasks"
```
