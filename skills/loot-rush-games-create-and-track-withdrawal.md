---
name: Create and track a LootRush crypto withdrawal
description: Initiate a cryptocurrency withdrawal for a user and poll its status.
api: openapi/loot-rush-games-partner-openapi.json
operations:
- createWithdrawal
- listWithdrawals
---

# Create and track a LootRush crypto withdrawal

Use the LootRush Partner API (host `https://third-party.lootrush.com`) to move
crypto out of a user's account and follow the transaction to completion.

## Auth
Send `Authorization: Bearer <token>` on every request. The token is the account
API key (Settings -> API Key) or one issued by a LootRush account manager.

## Steps
1. **Create the withdrawal** — `createWithdrawal` (`POST /api/crypto/{userId}/withdraw`).
   Provide the recipient address, currency ISO code and amount. Optionally pass an
   `externalId` for your own reconciliation. The withdrawal is created as a
   **queued transaction and processed asynchronously**, so a 200 means "accepted",
   not "settled".
2. **Poll status** — `listWithdrawals` (`GET /api/crypto/{userId}/withdraws`).
   Filter to the entry (by `externalId` or `id`) and read `status` and
   `transactionHash`. Repeat until the status is terminal.

## Error handling
- `401` invalid/missing bearer token, `403` not permitted, `404` unknown user or
  withdrawal. Errors return `{ "error": "<message>" }` — surface the message.
- There is **no documented idempotency key**; do not blindly retry a
  `createWithdrawal` that may have succeeded. Reconcile with `listWithdrawals`
  (matching your `externalId`) before re-issuing.
