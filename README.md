# bidsparq-mcp-server

Query [BidSparq](https://bidsparq.com)'s federal RFP, subaward, pricing, and vendor data from Claude Desktop, Claude Code, Cursor, Cline, and any other MCP-compatible AI client.

**Pro Max plan required.** Get one at [bidsparq.com/plan](https://bidsparq.com/plan).

## What you get

- Find primes who hire subcontractors in your NAICS (102K+ FFATA subaward records, 18-month window)
- Pricing percentiles (p25 / median / p75) + agency average + year-over-year trend
- Wired-RFP detection (brand-name lock, intent-to-award, thin PWS, competitor-specific language)
- Hybrid RFP search (keyword + semantic + full-text PDF) across 130+ sources
- Past contract awards, recompete tracking, agency profiles, seasonal patterns
- SAM-registered vendor lookup (NAICS, set-asides, certifications, UEI, location)

All 18 tools available from your AI client. Same 50 messages/day cap as in-app chat.

## Install

### 1. Get an API key

[bidsparq.com/settings/api-keys](https://bidsparq.com/settings/api-keys) → Create Key → copy the `bsq_live_...` value.

### 2. Add to your MCP client

#### Claude Desktop

Edit `~/Library/Application Support/Claude/claude_desktop_config.json` (macOS) or `%APPDATA%/Claude/claude_desktop_config.json` (Windows):

```json
{
  "mcpServers": {
    "bidsparq": {
      "command": "npx",
      "args": ["-y", "bidsparq-mcp-server"],
      "env": {
        "BIDSPARQ_API_KEY": "bsq_live_..."
      }
    }
  }
}
```

Restart Claude Desktop.

#### Claude Code (CLI)

Add to `.mcp.json` in your project (or `~/.claude/mcp.json` globally):

```json
{
  "mcpServers": {
    "bidsparq": {
      "command": "npx",
      "args": ["-y", "bidsparq-mcp-server"],
      "env": {
        "BIDSPARQ_API_KEY": "bsq_live_..."
      }
    }
  }
}
```

#### Cursor

Edit `.cursor/mcp.json` in your project:

```json
{
  "mcpServers": {
    "bidsparq": {
      "command": "npx",
      "args": ["-y", "bidsparq-mcp-server"],
      "env": {
        "BIDSPARQ_API_KEY": "bsq_live_..."
      }
    }
  }
}
```

#### Cline / RooCode / Continue

Same JSON shape — refer to your extension's MCP config docs and paste the snippet above.

## Try it

After install, ask your AI client:

- *"Find me primes who hire subs in NAICS 541512"*
- *"What's the typical award amount for VA software contracts?"*
- *"Show me wired RFPs in defense IT so I know what to avoid"*
- *"What contracts are coming up for recompete in cybersecurity?"*
- *"Tell me about NASA's buying patterns"*
- *"Find HUBZone businesses in Texas for NAICS 541512"*

## Configuration

| Env var | Required | Default | Notes |
|---|---|---|---|
| `BIDSPARQ_API_KEY` | yes | — | `bsq_live_...` from your settings page |
| `BIDSPARQ_BASE_URL` | no | `https://bidsparq.com` | Override for self-hosted / staging |

## Troubleshooting

- **`Invalid BIDSPARQ_API_KEY`** — Key is wrong, expired, or revoked. Create a new one in your settings.
- **`Pro Max plan required`** — Upgrade at [bidsparq.com/plan](https://bidsparq.com/plan).
- **`Daily message limit reached`** — Pro Max plans have 50 messages/day shared between in-app chat and MCP. Resets at midnight UTC.
- **`BidSparq MCP is not yet enabled in your account`** — MCP is in staged rollout. Email hello@bidsparq.com for early access.

## Privacy

- The MCP server only sends data you explicitly query for (NAICS, agency names, search terms).
- Your conversations stay in your AI client — BidSparq sees only the tool calls.
- API keys are bcrypt-hashed at rest; only the first 12 chars (`bsq_live_xxx`) are visible after creation.

## License

MIT. Source: https://github.com/n0edlg/bidsparq-mcp-server
