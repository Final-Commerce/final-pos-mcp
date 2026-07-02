# Building Final POS apps

You are connected to the **Final Flows MCP server** (`final-flows`), the
build API for [Final](https://finalpos.com) — a platform for creating custom
point of sale, kiosk, self-checkout, KDS, and in-person payment apps that run
on any hardware (desktop, tablet, mobile, payment terminal, line buster, menu
board). A second server, `final-docs`, answers questions about the Final POS
REST API (`https://api.finalpos.com`).

Everything you do through `final-flows` shows up live in the user's builder at
build.finalpos.com: the flow appears in their home grid, your session appears
as a chat in the rail, and every file write is a restorable checkpoint.

## The build loop

Follow this order for every POS app you build:

1. **`get_scaffold`** — call once per session to learn the app contract:
   engine-owned files (never write them), the baseline `package.json`, and the
   construction rules.
2. **`create_flow`** for a new app, or **`connect_chat`** to attach to an
   existing flow the user gives you. Never create a new flow when the user
   already has one.
3. **`log_message`** (role `"user"`) with the user's build brief, so their
   builder timeline stays faithful. Log short assistant notes at milestones.
4. **Ground before you write.** Every `renderClient` call must be verified
   with `lookup_command_frame` first — never guess a method signature, entity
   field, or pub/sub topic. Use `lookup_engine_component` for `./final/*`
   helpers and `lookup_pos_api` (or the `final-docs` server) for REST calls.
5. **Start from blueprints.** Check `list_blueprints` / `get_blueprint` for
   vetted screens (product grids, carts, payment surfaces, receipts) and adapt
   them instead of writing screens from scratch.
6. **`write_flow_files`** with full file contents. App code lives under
   `src/`, entry point `src/App.tsx`. Writes to engine-owned paths are
   rejected.
7. **`validate_flow`** and fix every finding — guardrails, command-frame
   typecheck, and secret scan must all pass.
8. **`start_preview`** and give the user the public preview URL so they can
   see the app live. Use `sync_preview` after edits (it is also the fastest
   compile check) and `get_preview_status` if something looks wrong.

## Shipping

When the user wants to go live, prefer **`open_in_builder`** — it returns a
deep link where they finish launch inside the Final builder (recommended).
`deploy_flow` is the direct CI-style path and needs an explicit
`hostingToken`; only use it when the user provides one.

## Rules that will save you

- Never write or delete engine-owned files listed by `get_scaffold`.
- Never invent `renderClient` APIs, entities, or topics — ground every call.
- Never hardcode secrets or API keys in flow files; `validate_flow` scans for
  them and will fail the build.
- Respect rate limits per API key: 30 preview boots/hour, 12 builds+deploys/
  hour, 300 file writes/hour. Batch your file writes.
- All tool calls are scoped to the user's own organization by their
  `FINAL_API_KEY`. If auth fails, tell them to mint a key at
  build.finalpos.com (chat rail > cable icon > "Connect your AI tool") and run
  `gemini extensions config final-pos`.
