# Installing Tastepix MCP (for AI agents)

Paste this file into your coding agent and ask it to set up Tastepix. Do each step, check it, and
stop at the first failure with a short explanation.

Tastepix is a hosted remote MCP server. There is nothing to build or run locally and no API key to
configure: add the server URL and complete the OAuth sign-in in the browser.

- Server URL: `https://mcp.tastepix.com/mcp`
- Transport: Streamable HTTP
- Auth: OAuth 2.1 (dynamic client registration). The first tool call or connect step opens a
  Tastepix sign-in page; the user signs in with Google or email and clicks Allow. Never ask the
  user for a password or an API key.

## Step 1: Connect the server

Use the route for this agent. If a `tastepix` server already points at `https://mcp.tastepix.com/mcp`,
keep it and move on.

- **Claude Code (plugin, recommended):** server plus the Tastepix skill in one install.

  ```
  /plugin marketplace add paablobello/tastepix-mcp
  /plugin install tastepix@tastepix
  ```

  Or the server alone: `claude mcp add --transport http --scope user tastepix https://mcp.tastepix.com/mcp`.
- **Codex:** `codex mcp add tastepix --url https://mcp.tastepix.com/mcp`, then `codex mcp login tastepix`.
  The plugin (server plus skill) is also available: `codex plugin marketplace add paablobello/tastepix-mcp`,
  then install **tastepix** from `/plugins`.
- **Gemini CLI:** `gemini extensions install https://github.com/paablobello/tastepix-mcp`
  (server plus the Tastepix context and skill).
- **VS Code:** `code --add-mcp '{"name":"tastepix","type":"http","url":"https://mcp.tastepix.com/mcp"}'`
- **Cursor / generic JSON** (`~/.cursor/mcp.json`, Cline `cline_mcp_settings.json`, etc.):

  ```json
  { "mcpServers": { "tastepix": { "url": "https://mcp.tastepix.com/mcp" } } }
  ```

  For clients that need an explicit transport type, use `"type": "http"` or `"type": "streamableHttp"`
  (whichever the client uses for Streamable HTTP) with the same URL.

## Step 2: Sign in

- Claude Code: `/mcp` → **tastepix** → sign in.
- Codex: `codex mcp login tastepix`.
- Other clients open the sign-in in the browser on the first Tastepix tool call.

Tell the user to finish the Tastepix sign-in in the browser.

## Step 3: Add the skill (optional)

The Claude Code plugin, the Codex plugin and the Gemini extension already include it. For other
agents:

```bash
npx skills add paablobello/tastepix-mcp
```

## Step 4: Verify

Call `list_models`. A list of models with prices means the server and sign-in work.

## Step 5: Done

Tell the user Tastepix is ready and suggest a first request, e.g. *"Generate an OG image for this
site and save it to `public/og.png`"*. Images are billed per image from the user's prepaid Tastepix
balance; new accounts get a small free credit.
