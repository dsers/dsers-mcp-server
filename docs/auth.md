# Authentication

DSers MCP uses OAuth 2.1 with PKCE. Configure only the remote MCP URL:

```text
https://ai.dsers.com/mcp
```

Do not configure an API key, copy an access token between clients, or manually add an `Authorization` header.

## Discovery and authorization

The protected resource publishes RFC 9728 metadata at:

```text
GET https://ai.dsers.com/.well-known/oauth-protected-resource
```

An unauthenticated MCP request returns `401`, a `WWW-Authenticate` header containing the protected-resource metadata URL, and a machine-readable body that directs the client to OAuth authorization.

The metadata identifies the authorization server and the OAuth scopes used by the currently registered tools. A compatible MCP client should discover the authorization server, open the DSers authorization page, complete authorization-code + PKCE, and then send the issued Bearer token on MCP requests.

## Roles and scopes

Authorization is enforced at two levels:

1. The HTTP entry point verifies the Bearer token through the configured DSers OAuth verification service.
2. Tool listing and execution enforce each tool's allowed roles and required OAuth scopes.

Registered tools support the `admin`, `dsers`, and `mcp` roles. External OAuth callers must also have the scopes required by the selected tool. Some tools resolve a narrower scope set from the actual arguments.

If scopes are missing, the tool returns `INSUFFICIENT_SCOPE` with `required_scopes`. Reauthorize the MCP connection; do not fabricate a token or retry unchanged credentials.

## Common failures

- `401 UNAUTHORIZED`: connect or reauthorize through the client UI.
- `403 FORBIDDEN`: the authenticated account or role cannot use the requested tool or resource.
- `INSUFFICIENT_SCOPE`: reauthorize with the returned scopes.
- `503 service_unavailable`: the verification service is temporarily unavailable; retry later rather than forcing a new login.

## Credential safety

Never put DSers passwords, MFA codes, backend API keys, store tokens, browser cookies, OAuth codes, access tokens, or refresh tokens in prompts, tool arguments, GitHub issues, or screenshots. Each MCP client should manage its own OAuth session.
