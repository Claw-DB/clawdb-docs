# Configuration

ClawDB reads configuration from environment variables, a `.clawdb.env` file, or a `config.toml` file. Environment variables always take precedence.

---

## Precedence order

```
CLI flags  >  Environment variables  >  .clawdb.env  >  config.toml  >  defaults
```

---

## Core variables

| Variable | Default | Description |
|---|---|---|
| `CLAWDB_URL` | — | Full URL of a running clawdb-server (e.g. `http://localhost:50050`) |
| `CLAWDB_API_KEY` | — | API key for cloud.clawdb.dev or a self-hosted server |
| `CLAWDB_AGENT_ID` | derived from hostname | Stable identifier for this agent instance |
| `CLAWDB_WORKSPACE_ID` | — | Workspace UUID. Required when using the cloud |
| `CLAWDB_DATA_DIR` | `~/.clawdb` | Local data directory for SQLite and binaries |
| `CLAWDB_LOG_LEVEL` | `info` | Log verbosity: `error`, `warn`, `info`, `debug` |
| `CLAWDB_LOG_FORMAT` | `json` | Log format: `json` or `console` |
| `CLAWDB_DEBUG` | `false` | Enable verbose debug output. **Never enable in production.** |

---

## Vector / embedding variables

| Variable | Default | Description |
|---|---|---|
| `CLAW_VECTOR_BASE_URL` | `http://localhost:8081` | URL of the claw-vector embedding service |
| `CLAW_VECTOR_ENABLED` | `true` | Set to `false` to disable semantic search (falls back to FTS5) |
| `CLAW_VECTOR_API_KEY` | — | API key for the vector service when `require_auth=true` |

---

## Guard / security variables

| Variable | Default | Description |
|---|---|---|
| `CLAW_GUARD_JWT_SECRET` | **required** | Secret used to sign session JWTs. Min 32 characters. |
| `CLAW_GUARD_POLICY_DIR` | — | Path to a directory of `.toml` policy files |
| `CLAW_GUARD_SENSITIVE_RESOURCES` | `""` | Comma-separated list of resource names that increase risk scores |

---

## Sync variables

| Variable | Default | Description |
|---|---|---|
| `CLAW_SYNC_HUB_URL` | — | URL of the sync hub (e.g. `https://eu-west-1.sync.clawdb.dev`) |
| `CLAW_SYNC_TLS_CERT_PATH` | — | Path to TLS certificate for hub connections |
| `CLAW_SYNC_TLS_KEY_PATH` | — | Path to TLS private key |
| `CLAW_SYNC_RETRY_BASE_MS` | `200` | Base delay in ms for exponential retry backoff |
| `CLAW_SYNC_MAX_RETRIES` | `3` | Maximum push retry attempts per chunk |

---

## Reflect variables

| Variable | Default | Description |
|---|---|---|
| `REFLECT_BASE_URL` | — | URL of the claw-reflect service |
| `REFLECT_API_KEY` | — | API key for the reflect service |
| `REFLECT_LLM_API_KEY` | — | Anthropic API key used by the reflect pipeline |
| `REFLECT_ENV` | `development` | Set to `production` to enforce security constraints |
| `REFLECT_DEBUG` | `false` | **Never set `true` in production — enforced by startup check** |

---

## config.toml reference

Create `~/.clawdb/config.toml` (or `.clawdb/config.toml` in your project):

```toml
# Core
data_dir      = "~/.clawdb"
log_level     = "info"
log_format    = "json"

# Cloud connection
workspace_id  = "your-workspace-uuid"
# api_key is read from CLAWDB_API_KEY env var — never put keys in this file

[vector]
enabled       = true
base_url      = "http://localhost:8081"

[guard]
policy_dir    = "~/.clawdb/policies"

[sync]
# hub_url is read from CLAW_SYNC_HUB_URL env var
retry_base_ms = 200
max_retries   = 3

[reflect]
# base_url is read from REFLECT_BASE_URL env var
```

---

## SDK-level configuration

Each SDK also accepts inline configuration that overrides environment variables:

{% tabs %}
{% tab title="TypeScript" %}
```typescript
import { ClawDB } from '@clawdb/sdk'

const db = await ClawDB.connect({
  endpoint:    'http://localhost:50050',
  apiKey:      process.env.CLAWDB_API_KEY,
  workspaceId: 'your-workspace-id',
  timeoutMs:   10_000,
  maxRetries:  3,
})
```
{% endtab %}

{% tab title="Python" %}
```python
from clawdb import ClawDB

db = await ClawDB.connect(
    endpoint="http://localhost:50050",
    api_key=os.environ["CLAWDB_API_KEY"],
    workspace_id="your-workspace-id",
    timeout_ms=10_000,
    max_retries=3,
)
```
{% endtab %}

{% tab title="Rust" %}
```rust
let db = ClawDB::new(ClawDBConfig {
    data_dir: PathBuf::from("~/.clawdb"),
    guard: GuardConfig {
        jwt_secret: std::env::var("CLAW_GUARD_JWT_SECRET")?.into(),
        ..Default::default()
    },
    ..Default::default()
}).await?;
```
{% endtab %}
{% endtabs %}

---

## Security checklist

{% hint style="danger" %}
Before deploying to production, verify these settings:

- `CLAW_GUARD_JWT_SECRET` is set and is at least 32 random characters
- `CLAWDB_DEBUG` is `false`
- `REFLECT_ENV` is `production`
- `CLAWDB_API_KEY` is **never** committed to source control — use `.env` files or secrets managers
- TLS is configured for the sync hub connection
{% endhint %}
