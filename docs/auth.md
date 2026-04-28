# Authentication

DSers MCP uses OAuth 2.1 + PKCE.

Do **not** configure API keys.  
Do **not** manually add an `Authorization` header.  
Do **not** paste DSers backend API keys, store tokens, or browser cookies into an MCP client.

## Remote Endpoint

```text
https://mcp.dsers.com/dropshipping/mcp
```

## Authentication Flow

1. Add the DSers MCP server URL in your MCP client.
2. On first tool use, the client opens the DSers authorization page.
3. Sign in with your DSers account and approve access.
4. Return to the MCP client and start calling tools.

## Requirements

- A DSers account
- At least one Shopify or Wix store connected in DSers
- An MCP client with remote HTTP MCP and OAuth 2.1 + PKCE support

## Important Notes

- If authorization fails, reconnect DSers MCP or re-run the login flow in your MCP client.
- For team environments, use a dedicated DSers account instead of sharing a personal account.
- Revoke access if you suspect an account session has been exposed.
- This public repository does not store user credentials, access tokens, browser cookies, or backend source code.
