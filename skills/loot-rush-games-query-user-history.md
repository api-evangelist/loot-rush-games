---
name: Query LootRush user transaction history
description: Retrieve a user's transaction and activity history via the History API.
api: openapi/loot-rush-games-partner-openapi.json
operations:
- getUserHistory
---

# Query LootRush user transaction history

Use the History API (host `https://history-api.lootrush.com`) to read the
authenticated user's transaction and activity history.

## Auth
`Authorization: Bearer <token>`. **The user is inferred from the API key** — you do
not pass a userId.

## Steps
1. **Fetch history** — `getUserHistory` (`GET /api/history`). Select the data set
   with the `resource` + `feature` query parameters.
2. **Handle pagination / limits** — the endpoint is rate limited to **2 requests
   every 2 seconds per user**; back off on `429`.

## Error handling
- `400` invalid `resource`/`feature` combination or query params.
- `401` invalid/missing bearer token.
- `429` rate limit exceeded — wait and retry.
- Errors return `{ "error": "<message>" }`.
