<p align="center">
  <img src="assets/final-icon.png" alt="Final" width="112" />
</p>

<h1 align="center">Final MCP server</h1>

<p align="center">
  Connect Claude, ChatGPT, Gemini, Cursor, Codex or any MCP client to <a href="https://finalpos.com">Final</a>:<br />
  run the store from <strong>Hub</strong> and build point-of-sale apps with <strong>Flow</strong>, through one server.
</p>

<p align="center">
  <code>https://ai.finalpos.com/mcp</code>
</p>

<p align="center">
  <a href="#connect">Connect</a> ·
  <a href="#what-your-agent-can-do">What it can do</a> ·
  <a href="TOOLS.md">Tool reference</a> ·
  <a href="#faq">FAQ</a> ·
  <a href="https://finalpos.com">finalpos.com</a>
</p>

---

**The Final MCP server is the official Model Context Protocol server for
[Final](https://finalpos.com), the commerce platform for custom point of sale.**
One remote endpoint, `https://ai.finalpos.com/mcp`, gives an AI agent access to
every Final product the merchant allows:

- **Hub**, the back office: products and catalog, stock and purchase orders,
  orders, customers, sales reports, team and roles, outlets, taxes, bookings.
- **Flow**, the app builder: create, preview, validate and deploy custom POS,
  kiosk, self-checkout and payment-terminal apps.

You sign in with your Final account (OAuth 2.1) and choose the company and what
the agent may do. There is nothing to install or run locally.

| | |
|---|---|
| Endpoint | `https://ai.finalpos.com/mcp` |
| Transport | Streamable HTTP |
| Auth | OAuth 2.1 with PKCE (sign in with Final), or a `fai_` connection key as `Authorization: Bearer` |
| Registry name | `com.finalpos/final` |
| Acts as | The person who connected it, on one company, within the packs they allowed |
| Cost | Free to connect; what the agent does follows your Final plan, as if you did it in Final |

## Connect

Most clients only need the URL. They open a Final sign-in page where you pick
the company and the packs, then they're connected.

### Claude (claude.ai, Claude Desktop)

**Settings → Connectors → Add custom connector**, URL `https://ai.finalpos.com/mcp`.

### ChatGPT

**Settings → Apps & Connectors → Advanced settings → Developer mode**, then
**Create** with the URL `https://ai.finalpos.com/mcp`.

### Claude Code

```bash
claude mcp add --transport http final https://ai.finalpos.com/mcp
```

Run `/mcp` in Claude Code and choose **Authenticate**. Or install the plugin,
which adds the server together with the Final playbook skill. Inside Claude
Code:

```
/plugin marketplace add Final-Commerce/final-pos-mcp
/plugin install final@final
```

### Gemini CLI

```bash
gemini extensions install https://github.com/Final-Commerce/final-pos-mcp
```

The extension adds the server, the Final playbook and commands:
`/final-pos:stock`, `/final-pos:sales`, `/final-pos:new`, `/final-pos:preview`,
`/final-pos:validate` and `/final-pos:deploy`. Sign in once with
`/mcp auth final` inside Gemini CLI. To add only the server:

```bash
gemini mcp add --transport http final https://ai.finalpos.com/mcp
```

### Cursor

[![Add to Cursor](https://cursor.com/deeplink/mcp-install-dark.svg)](https://cursor.com/en/install-mcp?name=final&config=eyJ1cmwiOiJodHRwczovL2FpLmZpbmFscG9zLmNvbS9tY3AifQ==)

Or in `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "final": { "url": "https://ai.finalpos.com/mcp" }
  }
}
```

### VS Code (GitHub Copilot)

[Install in VS Code](https://insiders.vscode.dev/redirect/mcp/install?name=final&config=%7B%22type%22%3A%22http%22%2C%22url%22%3A%22https%3A%2F%2Fai.finalpos.com%2Fmcp%22%7D), or:

```bash
code --add-mcp '{"name":"final","type":"http","url":"https://ai.finalpos.com/mcp"}'
```

### Codex, scripts and clients without OAuth

Create a connection key at [ai.finalpos.com/connections](https://ai.finalpos.com/connections).
It is shown once, and the page prints the exact snippet for each client. For
Codex, add this to `~/.codex/config.toml`:

```toml
[mcp_servers.final]
url = "https://ai.finalpos.com/mcp"
http_headers = { "Authorization" = "Bearer fai_YOUR_KEY" }
```

Any other client: `POST https://ai.finalpos.com/mcp` with
`Authorization: Bearer fai_YOUR_KEY`.

> Keys that start with `sk_` (Flow) or `fpm_` (Hub) belong to those products'
> own servers and are not accepted here. Create a `fai_` key instead, or use OAuth.

### Check that it works

Ask your agent: *"Use Final to tell me which company you're connected to and
which packs you can use."* It calls `get_connection` and `list_packs`.

## What your agent can do

Tools are grouped in **packs**. When you connect you choose which packs the
agent may use, and those are also the OAuth scopes. To keep the model's context
small, a connection lists only a few packs at a time. The core tools reach the
rest: `find_tools` searches every allowed pack, and `call_tool` calls any
allowed tool by name.

| Pack | Product | What it covers | Listed by default |
|---|---|---|:-:|
| `catalog` | Hub | Products, variants, categories, options, modifiers, product photos and the media library | ✓ |
| `inventory` | Hub | Stock levels and history, transfers, suppliers, purchase orders, reorder rules, stock CSV import/export | |
| `orders` | Hub | Orders, refunds and e-commerce order sync (read-only: the till takes payments) | |
| `customers` | Hub | Customer records, notes, order history and customer import | |
| `reports` | Hub | Financial summary, sales and payment breakdowns, employee sales, sessions, transactions, record export | |
| `team` | Hub | Team members, roles and permissions | |
| `store` | Hub | Outlets, stations, taxes and Hub extensions | |
| `bookings` | Hub | Bookable services: rules, resources, availability, holds and confirmations | |
| `build` | Flow | Build and deploy Final POS apps: API grounding, blueprints, flow files, preview sandboxes, validation, deploy | ✓ |
| `build_data` | Flow | Custom tables (the app database), the secrets vault and Flow extensions | |

Tool names carry their product: `hub_get_products`, `hub_find_low_stock`,
`flow_create_flow`, `flow_deploy_flow`. **[TOOLS.md](TOOLS.md) lists every
tool**, with its pack, whether it changes data, and its arguments.

**Core tools** (always listed): `get_connection`, `list_packs`, `find_tools`,
`call_tool`, `enable_packs`, `disable_packs`, `switch_company`.

### Things to ask

**Running the store (Hub)**
- "Which products are below their reorder point at the Main Street outlet? Draft a purchase order for the usual supplier."
- "Compare last week's sales by category with the week before."
- "Add these 40 products from the attached spreadsheet, with photos."
- "Who were my top ten customers this month, and what did they buy?"
- "Set up a 45-minute massage as a bookable service with two therapists."

**Building apps (Flow)**
- "Build a coffee-shop kiosk with a product grid, modifiers, a cart and card payments, then give me a preview link."
- "Add a tip screen to my counter app and validate it."
- "Ship the latest version of my self-checkout flow."

An app build usually runs `flow_get_scaffold` → `flow_create_flow` →
`flow_lookup_command_frame` (grounding) → `flow_get_blueprint` →
`flow_write_flow_files` → `flow_validate_flow` → `flow_start_preview`, and
returns a live preview URL. Everything the agent builds also appears in the
Final builder, where every file write is a restorable checkpoint.

## Safety and permissions

- **Your role still applies.** Every Hub call runs as the person who
  connected, and Hub checks their live role on each call. Owner-only actions stay
  owner-only.
- **Packs are the permission.** The agent can only call tools in the packs you
  allowed. You can change packs or disconnect at any time at
  [ai.finalpos.com/connections](https://ai.finalpos.com/connections), and
  disconnecting revokes every token at once.
- **Writes are labelled.** Read tools carry `readOnlyHint`, and deploys and
  deletes carry `destructiveHint`, so clients can ask before acting. The server
  tells agents to confirm deploys, deletes and bulk changes with you.
- **Money stays on the till.** Taking payments, refunding and voiding orders
  are not available over MCP.
- **Secrets never pass through the model.** `flow_request_secret` asks you to
  enter an API key in the builder.
- **Everything is logged.** The Connections page shows each connected client
  and every call it made, live.
- **Flow limits:** Flow allows 30 preview boots, 12 builds and deploys, and
  300 file writes per hour.

## Other Final MCP servers

| Server | URL | Use it for |
|---|---|---|
| Final docs | `https://mcp.finalpos.com/mcp` | Searching the Final POS REST API documentation, no account needed ([guide](https://finalpos.com/docs/ai-agents)) |
| Flow (direct) | `https://flow.finalpos.com/api/mcp` | Existing setups with a Flow `sk_` key. New setups should use `https://ai.finalpos.com/mcp`, which includes Flow. |

## FAQ

### What is the Final MCP server?
It is the official MCP server for Final POS. One remote URL,
`https://ai.finalpos.com/mcp`, lets an AI assistant read and manage a
merchant's Final back office (Hub) and build point-of-sale apps (Flow).

### Can ChatGPT or Claude manage my Final POS inventory?
Yes. Allow the `inventory` pack when you connect. The agent can check stock
levels, find low stock, suggest reorders, raise purchase orders and record
received goods.

### Can an AI agent build a POS app on Final?
Yes. With the `build` pack the agent creates a flow, writes the app against
Final's version-locked APIs and vetted screen blueprints, validates it, and
returns a live preview URL. You launch it from the Final builder.

### Do I need an API key?
No, if your client supports OAuth (Claude, ChatGPT, Claude Code, Cursor,
Gemini CLI, VS Code): just sign in with Final. Codex and scripts use a `fai_`
key from [ai.finalpos.com/connections](https://ai.finalpos.com/connections).

### Which company does it act on?
The one you pick when you connect. If your account has several, the agent can
call `switch_company`.

### Can the agent take payments or issue refunds?
No. Payments, refunds and voids happen on the till. Orders are read-only over
MCP.

### Why does my agent only see a few tools?
Clients put every listed tool into the model's context, and quality drops with
long lists. The agent finds the rest with `find_tools`, calls them with
`call_tool`, or lists a whole pack with `enable_packs`.

### Is there a cost?
Connecting is free. What the agent does is billed like the same work done in
Final yourself, under your Final plan.

## About this repository

This repo holds the public setup guide, the MCP Registry entries
(`server.json`, `legacy/`, `docs-server/`), the Gemini CLI extension
(`gemini-extension.json`, `GEMINI.md`, `commands/`), the Claude Code plugin
marketplace (`.claude-plugin/`, `plugins/final/`) and the generated
[tool reference](TOOLS.md). `GEMINI.md` and the Claude skill are the same
playbook, and CI checks that they match. The server itself is hosted by Final;
no code from this repo runs on your machine.

## License

[Apache-2.0](LICENSE) © Final Commerce
