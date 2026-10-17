---
name: final
description: Use when working with Final POS through the final MCP server — the merchant's catalog, stock, purchase orders, orders, customers, sales reports, team or bookings in Hub, or building, previewing and deploying a POS, kiosk or self-checkout app with Flow. Covers connecting, finding tools across packs, confirmation rules and the app build loop.
---
<!-- Same playbook as GEMINI.md (Gemini CLI). Change both. -->
# Working with Final

You are connected to the **Final MCP server** (`final`, `https://ai.finalpos.com/mcp`).
[Final](https://finalpos.com) is the commerce platform behind the merchant's
point of sale. The server reaches every Final product through one connection,
acting as the person who connected it, on one company, within the packs they
allowed:

- `hub_*` tools: **Hub**, the back office. Products, categories, modifiers,
  stock, suppliers and purchase orders, customers, orders, reports, team and
  roles, outlets, stations, taxes, media, bookings, extensions.
- `flow_*` tools: **Flow**, the app builder. Custom POS, kiosk, self-checkout
  and payment-terminal apps, with grounding, blueprints, preview sandboxes,
  validation and deploys.

## Start every session the same way

1. Call **`get_connection`**. It returns who you act as, the company Hub
   tools act on, the other companies you can switch to, and which packs are
   allowed and listed.
2. If no company is selected and you need Hub, call **`switch_company`**.
3. If the job needs tools you can't see, call **`find_tools`** with a plain
   description ("low stock", "purchase order", "deploy the app"). Call the
   match with **`call_tool`**, or **`enable_packs`** to list its whole pack.
   Don't tell the user a capability is missing until `find_tools` comes back
   empty.

Packs: `catalog`, `inventory`, `orders`, `customers`, `reports`, `team`,
`store`, `bookings` (Hub) and `build`, `build_data` (Flow). If a tool is in a
pack this connection doesn't allow, tell the user they can widen it at
https://ai.finalpos.com/connections.

## Rules

- **Confirm before changing live data.** Tools with `readOnlyHint: false`
  change the merchant's real store. Say what will change and wait for a yes
  before deploys, deletes, price changes and bulk imports. Reads need no
  confirmation.
- **Never invent ids.** Look up products, customers, outlets and flows first.
- **Money stays on the till.** You can't take payments, refund or void
  orders. Orders are read-only.
- **Secrets never pass through you.** Use `flow_request_secret` so the
  merchant enters API keys in the builder. Never write a key into a file.
- **Show your work in money terms.** When you report sales or stock, give the
  period, outlet and currency you used.

## Running the store (Hub)

- Catalog changes: search first (`hub_get_products`), then update. For many
  products, prefer the import tools (`hub_import_products`) over one call per
  product.
- Stock: `hub_find_low_stock` and `hub_get_reorder_suggestions` before you
  draft purchase orders, and `hub_record_received_goods` when a delivery
  arrives.
- Reports: `hub_get_financial_summary` and `hub_get_sales_breakdown` take a
  date range. State the range you chose.

## Building apps (Flow)

1. **`flow_get_scaffold`** once per session: the app contract, engine-owned
   files you must never write, and the construction rules.
2. **`flow_create_flow`** for a new app, or **`flow_connect_chat`** for one the
   user already has (`flow_list_flows`). Never create a duplicate.
3. **`flow_log_message`** (role `"user"`) with the brief, so the builder
   timeline matches the conversation.
4. **Ground before you write.** Check every `renderClient` call with
   `flow_lookup_command_frame`, `./final/*` helpers with
   `flow_lookup_engine_component`, and REST calls with `flow_lookup_pos_api`.
   Never guess a method, entity field or topic.
5. **Start from blueprints**: `flow_search_blueprints` / `flow_get_blueprint`
   for product grids, carts, payment screens and receipts.
6. **`flow_write_flow_files`** with full file contents. App code lives under
   `src/`, with `src/App.tsx` as the entry point. Batch your writes.
7. **`flow_validate_flow`**, and fix every finding until it passes.
8. **`flow_start_preview`**, then give the user the preview URL. After edits,
   **`flow_sync_preview`** (also the fastest compile check).
9. To go live, prefer **`flow_open_in_builder`**: the user launches from the
   Final builder. Use `flow_deploy_flow` only when they ask for it and confirm.

Flow allows 30 preview boots, 12 builds and deploys, and 300 file writes per
hour.

## When something fails

- `401` or "reconnect": the connection was revoked or expired. Ask the user to
  sign in again (Claude Code: `/mcp`; Gemini CLI: `/mcp auth final`) or check
  https://ai.finalpos.com/connections.
- "No company selected": `get_connection`, then `switch_company`.
- "pack … may not use": the merchant didn't allow that pack. Say which pack and
  where to allow it.
- Provider unreachable: `list_packs` shows what is unavailable right now.
  Retry later rather than working around it.
