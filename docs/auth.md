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

## OAuth Discovery Endpoints

The hosted server publishes the OAuth metadata required by remote MCP clients:

- `GET https://mcp.dsers.com/.well-known/oauth-protected-resource`
- `GET https://mcp.dsers.com/.well-known/oauth-authorization-server`
- `POST https://mcp.dsers.com/oauth/register`
- `GET https://mcp.dsers.com/oauth/authorize`
- `POST https://mcp.dsers.com/oauth/token`

The server supports Dynamic Client Registration, authorization-code flow with PKCE `S256`, refresh-token grant, and RFC 8707 resource binding for:

```text
https://mcp.dsers.com/dropshipping/mcp
```

MCP clients should let the OAuth flow run normally. Do not copy tokens between clients; each client manages its own OAuth session.

## ChatGPT App Notes

For ChatGPT Apps, use the same MCP endpoint:

```text
https://mcp.dsers.com/dropshipping/mcp
```

ChatGPT discovers OAuth through the protected-resource metadata and completes the authorization-code + PKCE flow. The current public submission target is data-only and does not require a widget iframe.

## Requirements

- A DSers account
- At least one Shopify, Wix, or WooCommerce store connected in DSers
- An MCP client with remote HTTP MCP and OAuth 2.1 + PKCE support

## Important Notes

- If authorization fails, reconnect DSers MCP or re-run the login flow in your MCP client.
- For team environments, use a dedicated DSers account instead of sharing a personal account.
- Revoke access if you suspect an account session has been exposed.
- This public repository does not store user credentials, access tokens, browser cookies, or backend source code.
