# Installing the Final MCP server (for AI agents)

Final hosts a **remote MCP server**, so there is nothing to build or run
locally. Add one URL to the user's MCP configuration:

```
https://ai.finalpos.com/mcp
```

- Transport: **Streamable HTTP**
- Auth: **OAuth 2.1 with PKCE**. The client discovers it from the `401`
  (`WWW-Authenticate` → `/.well-known/oauth-protected-resource/mcp`) and
  registers itself dynamically. The user signs in with their Final account and
  picks the company and the packs.
- The user needs a Final account (free at https://finalpos.com).

## 1. Add the server

If the host supports OAuth for remote servers, add it **without any headers**:

```json
{
  "mcpServers": {
    "final": { "type": "http", "url": "https://ai.finalpos.com/mcp" }
  }
}
```

Adjust the shape to the host: Gemini CLI uses `"httpUrl"`, VS Code uses
`"servers"` with `"type": "http"`, and Codex uses `[mcp_servers.final]` in
`~/.codex/config.toml`.

If the host does **not** support OAuth, ask the user to create a connection
key at https://ai.finalpos.com/connections (it is shown once) and send it as a
header:

```json
{
  "mcpServers": {
    "final": {
      "type": "http",
      "url": "https://ai.finalpos.com/mcp",
      "headers": { "Authorization": "Bearer fai_USER_KEY_HERE" }
    }
  }
}
```

Do not add an `Authorization` header when using OAuth: a configured header
stops the OAuth flow. Keys starting with `sk_` or `fpm_` are not accepted here.

## 2. Optional: the docs server

To search the Final POS REST API docs, also add
`https://mcp.finalpos.com/mcp` as `final-docs`.

## 3. Verify

Call `get_connection` on `final`. It returns the organization, the company Hub
tools act on, and the allowed and listed packs. Then call `list_packs`.

If a tool the user needs isn't listed, `find_tools` searches every allowed pack
and `call_tool` calls the match. See
https://github.com/Final-Commerce/final-pos-mcp for the playbook and
[TOOLS.md](TOOLS.md) for every tool.
