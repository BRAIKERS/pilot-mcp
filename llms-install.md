# Installing Pilot MCP (instructions for AI agents)

Pilot MCP is a hosted server. There is nothing to clone, build or install from this repository.

## Option 1: remote server (preferred when the client supports OAuth for remote servers)

Add this entry to the client's MCP settings:

```json
{
  "mcpServers": {
    "pilot": {
      "type": "streamableHttp",
      "url": "https://app.pilot.braike.com/mcp"
    }
  }
}
```

On first use the client opens Pilot's sign-in page in the browser (OAuth 2.1). The user signs in, or creates a workspace on the free plan, then picks the workspace to authorize.

## Option 2: local stdio bridge (works in any client that starts local programs)

Requires Node.js 20 or later.

```json
{
  "mcpServers": {
    "pilot": {
      "command": "npx",
      "args": ["-y", "@braike/pilot", "mcp"]
    }
  }
}
```

The bridge relays every request to `https://app.pilot.braike.com/mcp`. If nobody is signed in, the first request opens Pilot's sign-in in the browser. On a machine without a browser, ask the user to run `npx @braike/pilot login` in a terminal first.

## Checking the installation

- Ask the user to sign in when the browser opens.
- Call the `pilot_data_freshness` tool: it lists the connected platforms and the date of their latest figures.
- If no ad account is connected yet, tell the user to connect one in their Pilot workspace (Google Ads, Meta Ads, TikTok Ads and others).

No API key or environment variable is required. An access key can be passed instead of the browser sign-in with the `PILOT_API_KEY` environment variable on the stdio bridge.
