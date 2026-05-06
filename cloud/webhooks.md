# Webhooks

ClawDB cloud can POST to your endpoint when workspace events occur.

## Setting up a webhook

1. Go to [clawdb.dev/dashboard/webhooks](https://clawdb.dev/dashboard/webhooks)
2. Click **Add endpoint**
3. Enter your HTTPS URL
4. Select the events you want to receive
5. Save — a signing secret is generated automatically

## Available events

| Event | Fired when |
|---|---|
| `memory.written` | A memory is stored |
| `sync.completed` | A sync round completes |
| `reflect.run` | A reflect job finishes |
| `policy.denied` | The guard engine denies an operation |
| `usage.threshold` | Usage crosses 80% or 100% of a limit |
| `key.revoked` | An API key is revoked |
| `member.invited` | A team member is invited |

## Payload format

```json
{
  "id":          "evt_abc123",
  "type":        "memory.written",
  "workspace_id":"ws_xyz",
  "created_at":  "2026-05-01T14:30:00Z",
  "data": {
    "memory_id": "mem_def456",
    "agent_id":  "agent-prod"
  }
}
```

## Signature verification

Every delivery includes `X-ClawDB-Signature: sha256=...`. Verify it:

```typescript
import crypto from 'crypto'

const sig = req.headers['x-clawdb-signature']
const expected = 'sha256=' + crypto
  .createHmac('sha256', process.env.WEBHOOK_SECRET)
  .update(rawBody)
  .digest('hex')

if (!crypto.timingSafeEqual(Buffer.from(sig), Buffer.from(expected))) {
  return res.status(401).send('Invalid signature')
}
```

## Retry policy

Failed deliveries (non-2xx or timeout) are retried up to 3 times with exponential backoff. After 3 consecutive failures, the webhook is automatically disabled and you receive an email notification.

Replay any delivery from the webhook dashboard.
