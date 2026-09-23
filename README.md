# mcp-helius

Helius MCP — deeper Solana wallet/asset data than solscan (helius.dev)

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1663+ live data sources.

## Tools

| Tool | Description |
|------|-------------|
| `helius_parsed_transactions` | Parsed transaction history for a Solana address — human-readable, enriched tx feed (swaps, NFT sales, transfers) with a plain-English description, type, source protocol, fee, and timestamp for each transaction. Deeper than a raw signature list. Example: helius_parsed_transactions({ address: "86xCnPeV69n6t3DnyGvkKobf9FdN2H9oiVDdaMpo2MMY", limit: 20, _apiKey: "your-key" }) |
| `helius_assets_by_owner` | Tokens & NFTs owned by a Solana wallet — lists every digital asset (fungible tokens, regular + compressed NFTs) held by an address via the Digital Asset Standard (DAS), with name, symbol, balance, and interface type. Example: helius_assets_by_owner({ address: "86xCnPeV69n6t3DnyGvkKobf9FdN2H9oiVDdaMpo2MMY", _apiKey: "your-key" }) |
| `helius_asset` | Details for a Solana asset/mint — full Digital Asset Standard (DAS) record for a single token or NFT by its mint address: metadata (name/symbol/interface), current ownership, royalty config, and supply. Example: helius_asset({ id: "F9Lw3ki3hJ7PF9HQXsBzoY8GyE6sPoEZZdXJBsTTD2rk", _apiKey: "your-key" }) |

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "helius": {
      "url": "https://gateway.pipeworx.io/helius/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/helius/mcp` returns the tools in the table
above **plus the shared Pipeworx meta-tools** — `ask_pipeworx`,
`discover_tools`, `search_within`, `remember`/`recall` and the rest of the
gateway-wide set. So the tool count you see is larger than this table: a
single-pack endpoint currently lists roughly 30 shared tools alongside the
pack's own. The connection's `initialize` response states its exact scope, and
is the authoritative answer for a given day.

This is deliberate, not multiplexing by accident. The meta-tools are what let a
scoped connection answer a question this pack does not cover — via
`ask_pipeworx`, which routes across the whole catalog — without you adding a
second MCP server. There is currently no way to mount a pack endpoint without
them; if the extra schemas cost you more context than the routing is worth,
connect to the full gateway once rather than to several pack endpoints.

Or connect to the full Pipeworx gateway to get every pack's tools listed
directly, instead of just this one's:

```json
{
  "mcpServers": {
    "pipeworx": {
      "url": "https://gateway.pipeworx.io/mcp"
    }
  }
}
```

Both URLs reach the same gateway and the same 1663+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

This pack takes your own API key (`_apiKey`) — we don't front one for it, so there's no curl here that would run without it. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/helius_parsed_transactions`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Standalone (no gateway account)

This package also runs as a local stdio MCP server — no Pipeworx account, no
gateway round-trip:

```json
{
  "mcpServers": {
    "helius": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-helius"]
    }
  }
}
```

Or run it directly to confirm it starts:

```bash
npx -y @pipeworx/mcp-helius
```

It speaks MCP over stdin/stdout and answers `initialize`/`tools/list`/`tools/call`
for **only** this pack's tools — none of the shared meta-tools the gateway
connection above adds. Same source, same tools, no ask_pipeworx routing.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Helius data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
