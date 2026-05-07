# Hosted Service Notes

This public repository documents the DSers-hosted remote MCP server. It does not contain backend source code, self-hosting instructions, production deployment scripts, or secrets.

## Production Endpoint

```text
https://mcp.dsers.com/dropshipping/mcp
```

The production endpoint is operated by DSers. Users and reviewers should use this URL for ChatGPT App review, MCP registry metadata, and MCP-compatible clients.

## ChatGPT App Review Shape

The current ChatGPT App submission target is data-only:

- no iframe or widget resource
- no `dsers_app_render` tool
- normal MCP text / JSON tool results
- OAuth login through the DSers authorization flow

## Review Expectations

Before review, verify from outside DSers internal networks that:

- the production MCP URL is reachable over HTTPS
- unauthenticated MCP requests trigger OAuth discovery instead of a generic server error
- ChatGPT OAuth completes without API keys, cookies, MFA, SMS, email challenge, VPN, or manual token copying
- the tool list matches [`manifest.json`](../manifest.json) and [`chatgpt-app-submission.json`](../chatgpt-app-submission.json)

For normal documentation or connection issues, use GitHub Issues. For vulnerabilities, tokens, or account-specific data, use the private security contact in [`SECURITY.md`](../SECURITY.md).
