# DSers Official MCP Server

Official hosted Model Context Protocol server for operating DSers workflows from AI clients.

This public repository contains connection information, user documentation, examples, and registry metadata for the DSers-hosted service. It does **not** contain the production backend source code.

## Remote MCP Endpoint

```text
https://ai.dsers.com/mcp
```

The service uses Streamable HTTP and does not provide a local stdio server.

## Authentication

DSers MCP uses OAuth 2.1 with PKCE. Add only the server URL to your MCP client. Do not paste DSers passwords, API keys, store tokens, browser cookies, or a manually copied `Authorization` header into client configuration.

The protected resource publishes OAuth discovery metadata at:

```text
https://ai.dsers.com/.well-known/oauth-protected-resource
```

After authorization, available tools are filtered by the authenticated role and OAuth scopes. See [Authentication](docs/auth.md).

## Current Capabilities

The current service exposes 60 tools grouped around:

- account profile, plans, AI Credits, billing, and invoices
- connected stores, store connection, reauthorization, and Shopify Shipping Profiles
- supplier discovery, product search, product detail, and freight quotes
- pre-publish products, content, price, category, organization, and Pricing Rules
- managed store products, supplier Mapping, and AI-assisted Mapping
- publishing products to stores and checking asynchronous job status
- order search, diagnosis, editing, placement, payment, cancellation, and fulfillment
- packages, tracking numbers, buyer notifications, and supplier-product change notifications

The server advertises tools only. It does not expose MCP prompts, resources, or iframe widgets.

The complete current tool catalog is in [Tools](docs/tools.md).

## Quick Start

### Generic MCP configuration

```json
{
  "mcpServers": {
    "dsers": {
      "type": "http",
      "url": "https://ai.dsers.com/mcp"
    }
  }
}
```

On first connection, use the Sign in, Connect, Authorize, or Login action shown by your MCP client and complete the DSers authorization flow in the browser.

### Claude Code

```bash
claude mcp add dsers https://ai.dsers.com/mcp --transport http
claude mcp login dsers
```

### Codex CLI

```bash
codex mcp add dsers --url https://ai.dsers.com/mcp
codex mcp login dsers
```

### ChatGPT App

Add `https://ai.dsers.com/mcp` in ChatGPT Developer Mode or the Apps dashboard. The integration is data-only and returns normal MCP tool results without an iframe widget.

## Safety Model

Tools are classified as read, write, or dangerous. State-changing workflows use exact resource identifiers, ownership checks, OAuth scopes, stale-state checks where applicable, and explicit user confirmation before dangerous calls. A write whose upstream completion is uncertain is reported as non-retryable; clients must inspect current state instead of automatically repeating it.

Tool failures use structured error codes so clients can distinguish reauthorization, missing scopes, plan restrictions, rate limits, validation failures, and uncertain upstream writes.

## Documentation

- [Authentication](docs/auth.md)
- [Tools](docs/tools.md)
- [User guide](docs/user-guide.en.md)
- [Examples](docs/examples.md)
- [Privacy and Security](docs/privacy.md)
- [ChatGPT App submission notes](docs/chatgpt-app-submission.md)
- [ChatGPT App E2E playbook](docs/chatgpt-app-e2e-playbook.md)
- [Hosted service notes](docs/deployment.md)
- [中文说明](README.zh-CN.md)

## Registry and Review Files

- [`server.json`](server.json) — MCP Registry metadata
- [`manifest.json`](manifest.json) — public app metadata and tool inventory
- [`chatgpt-app-submission.json`](chatgpt-app-submission.json) — ChatGPT App review metadata and test cases

## License

This public documentation and metadata repository is licensed under the [Apache License 2.0](LICENSE).

## Support

For non-sensitive documentation, connection, client compatibility, or usage questions, open a [GitHub issue](https://github.com/dsers/dsers-mcp-server/issues).

For vulnerabilities, tokens, or account-specific data, contact `zhaohaoduo@dsers.com`.
