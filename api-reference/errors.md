# Error Codes

## HTTP status codes

| Status | Error code | Description |
|---|---|---|
| 400 | `validation_error` | Request body failed validation |
| 401 | `unauthorized` | Missing or invalid session token / API key |
| 402 | `plan_limit_reached` | Monthly operation limit exceeded |
| 403 | `permission_denied` | Guard engine denied the operation |
| 404 | `not_found` | Memory, branch, or resource does not exist |
| 409 | `conflict` | Concurrent write conflict |
| 413 | `request_too_large` | Request body exceeds 10 MB |
| 429 | `rate_limit_exceeded` | Too many requests — check `Retry-After` header |
| 500 | `internal` | Server error — contact support with the `requestId` |
| 503 | `service_unavailable` | Component temporarily unavailable |

## SDK error types

| Class | HTTP | Retry? |
|---|---|---|
| `ClawDBAuthError` | 401 | No |
| `ClawDBNotFoundError` | 404 | No |
| `ClawDBRateLimitError` | 429 | After `retryAfterMs` |
| `ClawDBUnavailableError` | 503 | Yes (automatic, 3×) |
| `ClawDBTimeoutError` | — | Yes (automatic, 3×) |
| `ClawDBError` | 500 | No |

## Plan limit error

```json
{
  "error":      "plan_limit_reached",
  "message":    "Monthly memory op limit exceeded",
  "statusCode": 402,
  "limit":      10000,
  "current":    10001,
  "upgradeUrl": "https://clawdb.dev/dashboard/billing"
}
```
