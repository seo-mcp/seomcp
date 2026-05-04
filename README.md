# SEOMCP

SEOMCP is an MCP server that gives AI agents direct access to backlink data,
domain authority scores, contact details, and page metadata for 67M+ domains
indexed from our own web crawl. Free tier available.

The server is hosted at **`https://seomcp.io/mcp`** (Streamable HTTP). This
repository is the public connection point for clients like Claude Desktop,
Claude Code, Cursor, and Windsurf.

- Web UI: https://seomcp.io
- REST API: https://seomcp.io/swagger
- Sign up (free tier): https://seomcp.io/Account/Register

## What you can do with it

| Tool | What it returns |
|---|---|
| `GetDomainOverview` | Domain Rank (0-100), backlinks, referring domains, country, category |
| `GetBacklinks` | List of backlinks for a target domain with anchor text and dofollow flag |
| `GetReferringDomains` | Aggregated list of domains linking to a target |
| `GetTopDomains` | Top-ranked domains filtered by country, category, and rank threshold |

A free API key gets you all of these with a daily quota that fits research and
small agent workloads. Higher tiers and details on the [pricing page](https://seomcp.io).

## Connect from Claude Desktop / Claude Code / Cursor / Windsurf

The hosted server uses the standard MCP Streamable HTTP transport. Most clients
ship with `mcp-remote` or equivalent, which proxies stdio to a remote endpoint.
Add this block to your client's MCP config:

```json
{
  "mcpServers": {
    "seomcp": {
      "command": "npx",
      "args": [
        "-y",
        "mcp-remote",
        "https://seomcp.io/mcp",
        "--header",
        "X-API-Key: ${SEOMCP_API_KEY}"
      ],
      "env": {
        "SEOMCP_API_KEY": "your_api_key_here"
      }
    }
  }
}
```

Replace `your_api_key_here` with the key you get after signing up at
https://seomcp.io/Account/Register.

### Where the config lives by client

- **Claude Desktop**: `~/Library/Application Support/Claude/claude_desktop_config.json` (macOS), `%APPDATA%\Claude\claude_desktop_config.json` (Windows)
- **Claude Code**: `~/.claude.json` (or `claude mcp add seomcp` in the CLI)
- **Cursor**: Settings, MCP, then add the same JSON
- **Windsurf**: Settings, Model Context Protocol, then add the same JSON

After saving, restart the client. The `seomcp` server should appear in the
available tools list.

## Direct API use without MCP

If you would rather call the REST API directly, the same key works:

```bash
curl -H "X-API-Key: your_api_key_here" \
  "https://seomcp.io/api/v1/domain/shopify.com"
```

Full reference: https://seomcp.io/swagger.

## Data sources

We process billions of pages from our monthly web crawl, extract backlinks and
domain signals, and run a weighted PageRank pipeline calibrated against
known-DR reference domains. The Domain Rank score is on the same 0-100 scale
you see in commercial SEO tools, with zero meaning no referring domains found
in the index. Everything ships through the same database that powers the web
UI, so the MCP and REST results match what you see when browsing.

## License

[MIT](./LICENSE).
