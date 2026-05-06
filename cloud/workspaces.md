# Workspaces & API Keys

## Workspaces

A workspace is the top-level container for an agent's data. All memories, branches, sync state, and policy rules belong to a workspace.

**Plan limits:**

| Plan | Workspaces |
|---|---|
| Free | 1 |
| Starter | 1 |
| Pro | 10 |
| Enterprise | Unlimited |

## API Keys

API keys authenticate your agent to ClawDB. Every key is:
- Generated with 32 random bytes, base64url-encoded
- Stored as a BLAKE3 hash — the raw key is shown **once only**
- Scoped to a single workspace

### Creating a key

1. Go to [clawdb.dev/dashboard/api-keys](https://clawdb.dev/dashboard/api-keys)
2. Click **Create key**
3. Give it a name (e.g. `agent-prod`)
4. Copy the raw key immediately — it will not be shown again

### Key rotation

Rotate keys from the API keys dashboard. Old keys remain valid for 24 hours after rotation to allow zero-downtime migration.

### Using a key

```bash
# Environment variable (recommended)
export CLAWDB_API_KEY=sk_live_your_key_here

# Or in .clawdb.env
CLAWDB_API_KEY=sk_live_your_key_here
```

{% hint style="danger" %}
Never commit API keys to source control. Use environment variables, `.env` files (git-ignored), or a secrets manager.
{% endhint %}
