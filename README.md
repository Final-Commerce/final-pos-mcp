<p align="center">
  <img src="assets/final-icon.png" alt="Final" width="128" />
</p>

<h1 align="center">Final POS MCP</h1>

<p align="center">
  Build custom <strong>point of sale, kiosk, self-checkout, KDS &amp; in-person payment apps</strong> with AI agents —<br />
  from Gemini CLI, Claude Code, Cursor, ChatGPT, or any MCP client.
</p>

<p align="center">
  <a href="https://finalpos.com">finalpos.com</a> ·
  <a href="https://build.finalpos.com">Final Builder</a> ·
  <a href="https://docs.finalpos.com">API Docs</a>
</p>

---

[Final](https://finalpos.com) lets anyone create their own POS — every pixel,
every permission, every interaction — and run it on any hardware: desktop,
tablet, mobile, kiosk, payment terminal, line buster, or menu board, with
integrated card payments in 24+ countries.

This repo is the official home for connecting **your own AI agent** to Final.
It ships:

- A **Gemini CLI extension** (this repo installs directly).
- The hosted **Final Flows MCP server** — 20 tools for grounded API lookups,
  vetted screen blueprints, flow file writes, live sandbox previews,
  validation gates, and deploys.
- The **Final docs MCP server** — search the Final POS REST API.
- Setup snippets for **Claude Code, Cursor, ChatGPT, and any MCP client**.

Everything your agent builds appears live in the [Final builder](https://build.finalpos.com):
the app shows up in your home grid, the agent's session appears as a chat, and
every file write is a restorable checkpoint.

## Get an API key

All clients authenticate with a Final **organization API key** (`sk_...`):

1. Sign in at [build.finalpos.com](https://build.finalpos.com).
2. Open a flow, then in the chat rail click the **cable icon** → **"Connect
   your AI tool"**.
3. Mint a key — it is shown **once**, so copy it right away.

## Install

### Gemini CLI

```bash
gemini extensions install https://github.com/Final-Commerce/final-pos-mcp
```

You'll be prompted for your `FINAL_API_KEY` during install (stored in your
system keychain). Change it later with:

```bash
gemini extensions config final-pos
```

Then start building:

```
/final-pos:new a coffee-shop kiosk with a product grid, cart, and card payments
/final-pos:preview   # live sandbox preview URL
/final-pos:validate  # run the full gate suite
/final-pos:deploy    # ship it
```

### Claude Code

```bash
claude mcp add final-flows --transport http https://build.finalpos.com/api/mcp \
  --header "Authorization: Bearer sk_YOUR_KEY"
```

### Cursor

[![Install MCP Server](https://cursor.com/deeplink/mcp-install-dark.svg)](https://cursor.com/en/install-mcp?name=final-flows&config=eyJ1cmwiOiJodHRwczovL2J1aWxkLmZpbmFscG9zLmNvbS9hcGkvbWNwIiwiaGVhZGVycyI6eyJBdXRob3JpemF0aW9uIjoiQmVhcmVyIFlPVVJfRklOQUxfQVBJX0tFWSJ9fQ==)

Or add to `~/.cursor/mcp.json` manually:

```json
{
  "mcpServers": {
    "final-flows": {
      "url": "https://build.finalpos.com/api/mcp",
      "headers": { "Authorization": "Bearer sk_YOUR_KEY" }
    }
  }
}
```

### ChatGPT and any other MCP client

The server speaks standard **Streamable HTTP**:

- **Endpoint:** `https://build.finalpos.com/api/mcp`
- **Auth header:** `Authorization: Bearer sk_YOUR_KEY` (or `x-api-key: sk_YOUR_KEY`)

In ChatGPT, add it as a connector under **Settings → Connectors** (developer
mode). Works the same in VS Code, Windsurf, Zed, and anything else that speaks
MCP.

## What your agent can do

| Category | Tools |
| --- | --- |
| **Grounding** | `lookup_command_frame`, `lookup_pos_api`, `lookup_engine_component`, `list_blueprints`, `get_blueprint`, `get_scaffold` |
| **Flow lifecycle** | `list_flows`, `create_flow`, `connect_chat`, `log_message`, `read_flow_files`, `write_flow_files`, `delete_flow_files` |
| **Preview & ship** | `start_preview`, `sync_preview`, `get_preview_status`, `stop_preview`, `validate_flow`, `open_in_builder`, `deploy_flow` |

A typical build: `get_scaffold` → `create_flow` → ground every UI call in
`lookup_command_frame` → adapt a vetted blueprint → `write_flow_files` →
`validate_flow` → `start_preview` → hand you a live URL.

All tool calls are scoped to your own organization's flows. Rate limits per
key: 30 preview boots/hour, 12 builds+deploys/hour, 300 file writes/hour.

## Security

- Your API key is org-scoped and revocable; the secret is never stored by
  this extension repo — Gemini CLI keeps it in your system keychain.
- The MCP servers are hosted by Final; no code from this repo runs locally
  except prompt/context files.
- Agents cannot touch engine-owned files, and every deploy passes guardrail,
  typecheck, and secret scans.

## License

[Apache-2.0](LICENSE) © Final Commerce
