# Security Policy

DSers MCP is a hosted remote MCP server operated by DSers at:

```text
https://ai.dsers.com/mcp
```

This public repository contains documentation and metadata only. It does not contain the production backend, credentials, OAuth tokens, browser cookies, private API keys, or customer data.

## Authentication

DSers MCP uses OAuth 2.1 with PKCE. Clients should discover authorization through `/.well-known/oauth-protected-resource`; users must not paste passwords, MFA codes, API keys, store tokens, cookies, or manual Authorization headers into MCP configuration.

The MCP BFF validates Bearer tokens and enforces tool roles and OAuth scopes. Business services continue to enforce account ownership for requested resources.

## Safe operation

- Copy exact store, product, supplier, order, task, and variant identifiers from DSers tool results.
- Preview current state before dangerous writes and obtain explicit user confirmation immediately before invocation.
- Use the latest resource version for versioned mutations.
- Never automatically retry an uncertain write; inspect current state first.
- Treat asynchronous task creation as pending until `get_job_status` reports a terminal result.

## Reporting security issues

For non-sensitive documentation or connection questions, open a [GitHub issue](https://github.com/dsers/dsers-mcp-server/issues).

For vulnerabilities, secrets, tokens, or account-specific data, email `zhaohaoduo@dsers.com`. Include the affected endpoint or tool, impact, reproduction steps, and relevant timestamps. Redact tokens, passwords, cookies, addresses, and customer data.
