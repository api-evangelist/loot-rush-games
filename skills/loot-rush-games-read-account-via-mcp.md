---
name: Read a LootRush account read-only via MCP
description: Use the official LootRush MCP server to inspect balances, cards and history.
api: openapi/loot-rush-games-partner-openapi.json
operations:
- callMcpTool
---

# Read a LootRush account read-only via MCP

LootRush ships an **official hosted MCP server** at `https://mcp.lootrush.com/mcp`
(Streamable HTTP, JSON-RPC 2.0, stateless). All tools are read-only and scoped to
the authenticated user's own data.

## Auth
`Authorization: Bearer <API_KEY>` (or `?token=` query param). The key must carry
the `mcp` scope; `mcp_card_reveal` is additionally required to reveal card secrets.

## Connect (Claude Code)
```
claude mcp add --transport http lootrush https://mcp.lootrush.com/mcp \
  --header "Authorization: Bearer YOUR_API_KEY"
```

## Tools (call via `callMcpTool`, `POST /mcp`, method `tools/call`)
- `getAccountBalance` — total USD value with per-token/network breakdown.
- `getAccountHistory` — deposits, withdrawals, converts, sends, card ops by date or id.
- `getUserCards` — cards across issuers with balances (optionally reveal number/CVV; needs `mcp_card_reveal`).
- `getUserCardTransactions` — card transactions filtered by card/date/status/polarity.
- `getCardRevealPublicKey` — public key to decrypt revealed card secrets.

## Notes
- Set `params.name` to one of the tools above; the payload returns JSON-encoded.
- Errors follow JSON-RPC 2.0: `{ jsonrpc, id, error: { code, message } }` (e.g. `-32001 Unauthorized`).
- Respect rate limits — the endpoint returns `429` when exceeded.
