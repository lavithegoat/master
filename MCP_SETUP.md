# MCP Server Setup

This repo ships a project-scoped MCP configuration in [`.mcp.json`](./.mcp.json).
When you open the project in Claude Code, you'll be prompted to approve these
servers before they connect.

## Servers

| Server | Transport | Auth | Notes |
| --- | --- | --- | --- |
| **playwright** | stdio (`npx @playwright/mcp@latest`) | none | Browser automation. First run downloads the package. |
| **composio** | http (`https://connect.composio.dev/mcp`) | OAuth | Run `claude mcp login composio` (or `/mcp` in the TUI) and sign in. |
| **perplexity** | http (`https://api.perplexity.ai/mcp`) | Bearer token | Reads the key from the `PERPLEXITY_API_KEY` environment variable. |
| _firecrawl_ | _http_ | _API key_ | _Not configured — add when a key is available (see below)._ |

## Secrets

No secrets are committed. The Perplexity key is referenced as
`${PERPLEXITY_API_KEY}` and expanded from your environment at load time. Set it
before starting Claude Code, e.g.:

```bash
export PERPLEXITY_API_KEY="pplx-..."
```

## Authenticating Composio

Composio uses OAuth, which requires an interactive session:

```bash
claude mcp login composio
```

or open the `/mcp` menu inside the Claude Code TUI and authorize it there.

## Adding Firecrawl later

Firecrawl was skipped during setup. To add it once you have a free key from
[firecrawl.dev](https://firecrawl.dev), add a block to `.mcp.json`:

```json
"firecrawl": {
  "type": "http",
  "url": "https://mcp.firecrawl.dev/v2/mcp",
  "headers": { "Authorization": "Bearer ${FIRECRAWL_API_KEY}" }
}
```

and export `FIRECRAWL_API_KEY` in your environment.

## Verifying connections

```bash
claude mcp list          # health-checks all approved servers
claude mcp get <name>    # details + status for one server
```

Remote servers can only be health-checked from an environment whose network
policy allows outbound access to their hosts.
