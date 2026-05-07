# DSers Official MCP Server

Official DSers MCP Server for AI-powered dropshipping automation.

The DSers Official MCP Server is a hosted remote Model Context Protocol server that allows MCP-compatible AI clients and ChatGPT Apps to work with DSers dropshipping workflows, including product search, product import, product optimization, variant editing, pricing rules, store publishing, and supplier replacement.

This repository does **not** contain the DSers MCP server backend source code. It provides official public documentation, connection information, examples, and registry metadata for the DSers-hosted remote MCP server.

## Remote MCP Endpoint

```text
https://mcp.dsers.com/dropshipping/mcp
```

## Transport

Remote HTTP / Streamable HTTP.

This server does not provide a local stdio server.

## Authentication

DSers MCP uses OAuth 2.1 + PKCE.

Do **not** configure API keys.  
Do **not** manually add an `Authorization` header.  
Do **not** paste DSers backend API keys, store tokens, or browser cookies into an MCP client.

On first tool use, supported MCP clients open the DSers authorization page. After the user signs in and approves access, the client can call DSers MCP tools.

For details, see [Authentication](docs/auth.md).

## Requirements

You need:

- A DSers account
- At least one Shopify or Wix store connected in DSers
- An MCP client that supports remote HTTP MCP and OAuth 2.1 + PKCE

Verified clients (tested end to end): ChatGPT Developer Mode, Claude Desktop, Cursor, Claude Code, Codex CLI, OpenClaw.

Expected to work (any MCP client with remote HTTP + OAuth 2.1 + PKCE support): VS Code, Cline, Windsurf, Zed, Continue.

## What You Can Do

- Import products from AliExpress, Alibaba, 1688, Accio, or supported supplier sources
- Apply pricing, title, description, image, and variant rules
- Preview DSers drafts before pushing to Shopify or Wix
- Search the DSers product pool
- Browse DSers import-list drafts and already pushed products
- Replace suppliers on existing store products with SKU-level matching

## Quick Start

### Claude Desktop

Add this to `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "dsers": {
      "type": "http",
      "url": "https://mcp.dsers.com/dropshipping/mcp"
    }
  }
}
```

Restart Claude Desktop. On first tool use, the client starts OAuth and opens the DSers login page.

### Claude Code

```bash
claude mcp add dsers https://mcp.dsers.com/dropshipping/mcp --transport http
claude mcp login dsers
```

### Cursor

Open:

```text
Settings → Tools & Integrations → MCP Tools → Add / Connect
```

Use this server URL:

```text
https://mcp.dsers.com/dropshipping/mcp
```

Cursor should start the OAuth flow automatically.

### Codex CLI

After adding the MCP server, run:

```bash
codex mcp login dsers
```

If the client asks for the server URL, use:

```text
https://mcp.dsers.com/dropshipping/mcp
```

### ChatGPT App

Use the remote endpoint in ChatGPT Developer Mode or the Apps dashboard:

```text
https://mcp.dsers.com/dropshipping/mcp
```

The current ChatGPT submission target is data-only: DSers exposes normal MCP tools and does not require an iframe widget. See [ChatGPT App submission notes](docs/chatgpt-app-submission.md).

### OpenClaw

```bash
openclaw mcp set dsers '{"url":"https://mcp.dsers.com/dropshipping/mcp","transport":"streamable-http"}'
```

Transport and authorization support may vary by OpenClaw deployment. Follow the connection guide for your current environment.

## Documentation

- [Authentication](docs/auth.md)
- [Tools](docs/tools.md)
- [Privacy and Security](docs/privacy.md)
- [ChatGPT App submission notes](docs/chatgpt-app-submission.md)
- [ChatGPT App E2E playbook](docs/chatgpt-app-e2e-playbook.md)
- [Hosted deployment notes](docs/deployment.md)
- [Examples](docs/examples.md)
- [中文说明](README.zh-CN.md)

## Example MCP Client Configuration

```json
{
  "mcpServers": {
    "dsers": {
      "type": "http",
      "url": "https://mcp.dsers.com/dropshipping/mcp"
    }
  }
}
```

A minimal example is also available at [`examples/remote-mcp.json`](examples/remote-mcp.json).

## Registry and Review Files

- [`server.json`](server.json) - MCP registry metadata for the hosted remote server
- [`manifest.json`](manifest.json) - public app metadata, tool list, prompts, and privacy URL
- [`chatgpt-app-submission.json`](chatgpt-app-submission.json) - ChatGPT App review helper with tool hints and test prompts

## License

This public documentation and metadata repository is licensed under the [Apache License 2.0](LICENSE).

## Support

For non-sensitive documentation, connection, client compatibility, or usage questions, open a GitHub issue:

```text
https://github.com/dsers/dsers-mcp-server/issues
```

For vulnerabilities, tokens, or account-specific data, contact:

zhaohaoduo@dsers.com
