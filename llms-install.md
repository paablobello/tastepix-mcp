# Installing Tastepix MCP (for AI agents)

Tastepix is a hosted remote MCP server. There is nothing to install or build locally and no API key
to configure: add the server URL and complete the OAuth sign-in in the browser.

- Server URL: `https://mcp.tastepix.com/mcp`
- Transport: Streamable HTTP
- Auth: OAuth 2.1 (dynamic client registration). The first tool call or connect step opens a
  Tastepix sign-in page; the user signs in with Google or email and clicks Allow.

Client config examples:

- Claude Code: `claude mcp add --transport http --scope user tastepix https://mcp.tastepix.com/mcp`, then `/mcp` → tastepix → sign in.
- Codex: `codex mcp add tastepix --url https://mcp.tastepix.com/mcp` then `codex mcp login tastepix`.
- Cursor / generic JSON (`mcp.json`, Cline `cline_mcp_settings.json`, etc.):

```json
{ "mcpServers": { "tastepix": { "url": "https://mcp.tastepix.com/mcp" } } }
```

For clients that need an explicit transport type, use `"type": "streamableHttp"` (or the client's
equivalent for Streamable HTTP) with the same URL.

After connecting, verify by calling `list_models`. Image generation is billed per image from the
user's prepaid Tastepix balance; new accounts get a small free credit.
