# Environment Variables

Full reference for self-hosted deployments. See [Configuration](../getting-started/configuration.md) for the agent-side variables.

## API server (`apps/api/`)

| Variable | Required | Description |
|---|---|---|
| `DATABASE_URL` | ✓ | PostgreSQL connection string |
| `REDIS_URL` | ✓ | Redis connection string |
| `JWT_SECRET` | ✓ | Min 32 chars. Signs access tokens |
| `JWT_REFRESH_SECRET` | ✓ | Min 32 chars. Signs refresh tokens |
| `STRIPE_SECRET_KEY` | ✓ | Stripe secret key for billing |
| `STRIPE_WEBHOOK_SECRET` | ✓ | Stripe webhook signature secret |
| `RESEND_API_KEY` | ✓ | Resend API key for transactional email |
| `ANTHROPIC_API_KEY` | ✓ | Anthropic API key for the AI assistant |
| `APP_URL` | ✓ | Public URL of the web app (e.g. `https://clawdb.dev`) |
| `API_URL` | ✓ | Public URL of the API (e.g. `https://api.clawdb.dev`) |
| `ALLOWED_ORIGINS` | ✓ | Comma-separated CORS allowed origins |
| `PORT` | — | Default: 3001 |
| `NODE_ENV` | — | Default: `development` |
| `SWAGGER_ENABLED` | — | Default: `false`. Set `true` to enable /api/docs |

## Worker (`apps/worker/`)

Workers share most env vars with the API server. Additional:

| Variable | Required | Description |
|---|---|---|
| `WORKER_CONCURRENCY` | — | Default: 10 concurrent jobs per queue |

## Reflect service (`claw-reflect/`)

| Variable | Required | Description |
|---|---|---|
| `REFLECT_DATABASE_URL` | ✓ | PostgreSQL connection string |
| `REFLECT_REDIS_URL` | ✓ | Redis connection string |
| `REFLECT_LLM_API_KEY` | ✓ | Anthropic API key |
| `REFLECT_ENV` | ✓ | Set `production` to enforce security constraints |
| `REFLECT_RATE_LIMIT_ENABLED` | — | Default: `true` |
