# API Keys

## Key format

All ClawDB API keys follow this format:

```
sk_live_<random_base64url>
```

The `sk_live_` prefix identifies it as a production key. Test keys (if applicable) use `sk_test_`.

## Storage

Raw API keys are **never stored** by ClawDB. Only the BLAKE3 hash is stored in the database. The raw key is shown exactly once at creation — if you lose it, create a new key.

## Scopes

Keys can be restricted to specific operation scopes:

| Scope | Permission |
|---|---|
| `*` | All operations (default) |
| `memory:read` | Search and recall only |
| `memory:write` | Store and delete memories |
| `branch:*` | All branch operations |
| `sync:push` | Push to sync hub |
| `sync:pull` | Pull from sync hub |

## Rotation

1. Create a new key in the dashboard
2. Update your environment variable
3. Revoke the old key (remains valid 24h after revocation for zero-downtime)

## Best practices

- Use one key per environment (dev, staging, prod)
- Use one key per agent when operating a multi-agent system
- Restrict scopes to the minimum needed
- Rotate keys every 90 days
- Store in a secrets manager (AWS Secrets Manager, Doppler, 1Password Secrets Automation)
