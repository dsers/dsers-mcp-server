# Security Policy

DSers MCP is a hosted remote MCP server operated by DSers at:

```text
https://mcp.dsers.com/dropshipping/mcp
```

This public repository contains documentation and metadata only. It does not contain backend source code, production secrets, OAuth tokens, browser cookies, private API keys, or user data.

## Authentication

DSers MCP uses OAuth 2.1 + PKCE. Users authorize through DSers OAuth. MCP clients should not ask users to paste DSers passwords, MFA codes, backend API keys, store tokens, browser cookies, or manual Authorization headers.

The hosted server publishes:

- `/.well-known/oauth-protected-resource`
- `/.well-known/oauth-authorization-server`
- `/oauth/register`
- `/oauth/authorize`
- `/oauth/token`

## Data Handling

The hosted service processes DSers account, store, product, supplier, and operational metadata only as needed for user-requested workflows. Product and store data is processed during requests and is not stored in this public repository.

See [Privacy and Security](docs/privacy.md) and the hosted policy at:

```text
https://mcp.dsers.com/privacy-policy
```

## Reporting Security Issues

Do not open a public GitHub issue for vulnerabilities, secrets, tokens, or account-specific data. Contact:

```text
zhaohaoduo@dsers.com
```

Include:

- affected endpoint or tool name
- impact summary
- reproduction steps
- relevant timestamps
- whether any account data or tokens may have been exposed

Do not include OAuth tokens, passwords, cookies, or full customer data in the report. Redact sensitive fields.

## Safe Use Guidance

- Use OAuth instead of API keys or cookies.
- Use `backend_only` for store pushes unless the user explicitly requests live publishing.
- Require confirmation before `dsers_store_push`, `dsers_product_delete`, or `dsers_sku_remap mode=apply`.
- Preview supplier replacement with `dsers_sku_remap mode=preview` before applying it.
- Revoke and reconnect the MCP session if a client device or OAuth session may be compromised.
