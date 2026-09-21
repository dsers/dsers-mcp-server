# Hosted Service Notes

This public repository documents the DSers-hosted remote MCP server. It does not contain backend source code, self-hosting instructions, production deployment scripts, or secrets.

## Production endpoint

```text
https://ai.dsers.com/mcp
```

The service uses Streamable HTTP and is operated by DSers.

## Protocol surface

The current server is data-only:

- 60 MCP tools
- no MCP prompts
- no MCP resources
- no iframe or widget tool
- OAuth discovery through Protected Resource Metadata

## Release verification

Before publishing registry or ChatGPT App metadata, verify from outside DSers internal networks that:

- the production URL is reachable over HTTPS;
- unauthenticated requests return OAuth discovery information;
- OAuth completes without API keys, cookies, manual token copying, VPN, or internal-network access;
- the runtime tool names match `manifest.json` and `chatgpt-app-submission.json`;
- read, write, dangerous, and open-world annotations match the deployed schemas;
- the deployed MCP server version is reflected consistently in public release metadata.

For normal documentation or connection issues, use GitHub Issues. For vulnerabilities, tokens, or account-specific data, use the private contact in [`SECURITY.md`](../SECURITY.md).
