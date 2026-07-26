# Installing the Final POS MCP server (for AI agents)

Final POS ships a **hosted remote MCP server** — there is nothing to build or run
locally. You only need to add the endpoint to the user's MCP configuration.

## 1. Ask the user for their API key

The server requires a Final organization API key (`sk_...`). Ask the user to:

1. Sign in at https://build.finalpos.com
2. Open a flow, then in the chat rail click the **cable icon** → **"Connect your AI tool"**
3. Mint a key and paste it to you (it is shown once)

If the user has no Final account yet, they can create one free at
https://build.finalpos.com.

## 2. Add the server configuration

Add this to the MCP settings file (adjust to the host application's format):

```json
{
  "mcpServers": {
    "final-flows": {
      "url": "https://build.finalpos.com/api/mcp",
      "headers": {
        "Authorization": "Bearer sk_USER_KEY_HERE"
      }
    },
    "final-docs": {
      "url": "https://docs.finalpos.com/mcp"
    }
  }
}
```

- Transport: **Streamable HTTP** (both servers)
- `final-flows` auth: `Authorization: Bearer sk_...` header (or `x-api-key: sk_...`);
  clients with OAuth 2.1 support can instead connect with no header and complete
  the OAuth flow.
- `final-docs` needs no authentication.

## 3. Verify

Call the `get_scaffold` tool on `final-flows` — it should return the Final app
contract. On `final-docs`, search for "orders" — it should return REST API
documentation results.
