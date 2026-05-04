# SEOMCP

SEOMCP is an MCP server that gives AI agents direct access to backlink data,
domain authority scores, contact details, and page metadata for 67M+ domains
indexed from our own web crawl. Free tier available.

The server is hosted at **`https://seomcp.io/mcp`** (Streamable HTTP). This
repository is the public connection point for clients like Claude Desktop,
Claude Code, Cursor, and Windsurf.

- Web UI: https://seomcp.io
- REST API reference: https://seomcp.io/swagger
- Sign up (free tier): https://seomcp.io/Account/Register

## What you can do with it

Eight tools, all read-only:

| Tool | Purpose |
|---|---|
| `get_domain_overview` | Domain Rank (0-100), backlinks, referring domains, country, category |
| `check_domain_authority` | Rank score, referring domains, dofollow ratio |
| `get_backlinks` | Backlinks pointing to a domain with anchor text and dofollow flag |
| `get_referring_domains` | Aggregated list of unique domains linking to a target |
| `get_top_domains` | Top-ranked domains, optionally filtered by country and category |
| `get_domain_pages` | Page-level SEO metadata (titles, descriptions, h1s, status codes) |
| `get_domain_contacts` | Emails, phones, addresses, and social profiles for a domain |
| `get_domain_history` | Backlink trend over time for a domain |

## Connect from Claude Desktop / Claude Code / Cursor / Windsurf

You need an API key first. Sign up at https://seomcp.io/Account/Register, copy
the key from your dashboard. The free tier is enough for research and small
agent workloads. The same key also works against the REST API.

The hosted server uses standard MCP Streamable HTTP, and the universal
`mcp-remote` proxy from npm bridges stdio to the remote endpoint, so no install
is needed beyond what `npx` does on demand. Add this block to your client's MCP
config:

```json
{
  "mcpServers": {
    "seomcp": {
      "command": "npx",
      "args": [
        "-y", "mcp-remote",
        "https://seomcp.io/mcp",
        "--header", "X-API-Key:YOUR_API_KEY_HERE"
      ]
    }
  }
}
```

Replace `YOUR_API_KEY_HERE` with the key from your dashboard. Note there is no
space after the colon in the header value — `mcp-remote` parses the argument
strictly.

### Where the config lives by client

- **Claude Desktop**: `~/Library/Application Support/Claude/claude_desktop_config.json` (macOS), `%APPDATA%\Claude\claude_desktop_config.json` (Windows)
- **Claude Code**: `~/.claude.json`, or run `claude mcp add seomcp` in the CLI
- **Cursor**: Settings, MCP servers, Add new
- **Windsurf**: Settings, Model Context Protocol, Add new

After saving, restart the client. The `seomcp` server should appear in the
available tools list.

## Direct REST use without MCP

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
