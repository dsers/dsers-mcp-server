# DSers Official MCP Server

DSers 官方 MCP Server，用于 AI 驱动的 dropshipping 自动化。

DSers Official MCP Server 是一个托管版远程 Model Context Protocol 服务，可让支持 MCP 的 AI 客户端和 ChatGPT Apps 调用 DSers 的选品、导入、商品优化、变体编辑、定价规则、店铺推送、供应商替换等工作流。

本仓库**不包含 DSers MCP Server 后端源码**。本仓库只提供官方公开文档、连接信息、配置示例和 registry metadata，用于发布和接入 DSers 托管的远程 MCP 服务。

## Remote MCP Endpoint

```text
https://mcp.dsers.com/dropshipping/mcp
```

## Transport

Remote HTTP / Streamable HTTP。

这个 MCP 是远程 HTTP 服务，不提供本地 stdio server。

## 认证方式

DSers MCP 使用 OAuth 2.1 + PKCE。

不要手动配置 API key。  
不要手动写 `Authorization` header。  
不要把 DSers 后台 API key、店铺 token 或浏览器 cookie 粘贴到 MCP 客户端。

首次调用工具时，支持的 MCP 客户端会打开 DSers 授权页面。用户登录并授权后，即可调用 DSers MCP tools。

详细说明见：[认证说明](docs/zh-CN/auth.md)。

## 前置条件

使用者需要：

- 一个可用的 DSers 账号
- DSers 已连接至少一个 Shopify、Wix 或 WooCommerce 店铺
- 一个支持远程 HTTP MCP 和 OAuth 2.1 + PKCE 的 MCP 客户端

已验证客户端（端到端测试通过）：ChatGPT Developer Mode、Claude Desktop、Cursor、Claude Code、Codex CLI。

预期可用（具备远程 HTTP + OAuth 2.1 + PKCE 支持的 MCP 客户端）：VS Code、Cline、Windsurf、Zed、Continue。OpenClaw 支持情况取决于具体部署，部分用户可通过 registry 或社区网关接入。

## 可实现能力

- 从 AliExpress、Alibaba、1688、Accio 或支持的供应商来源导入商品
- 给商品应用定价、标题、描述、图片、变体规则
- 预览 DSers draft，再推送到已连接店铺
- 搜索 DSers 商品池
- 浏览 DSers import list 和已推送商品
- 对已上架商品做 SKU 级供应商替换

## 快速开始

### Claude Desktop

在 `claude_desktop_config.json` 添加：

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

重启 Claude Desktop。首次调用工具时，客户端会触发 OAuth 并打开 DSers 登录页。

### Claude Code

```bash
claude mcp add dsers https://mcp.dsers.com/dropshipping/mcp --transport http
claude mcp login dsers
```

### Cursor

打开：

```text
Settings → Tools & Integrations → MCP Tools → Add / Connect
```

填入：

```text
https://mcp.dsers.com/dropshipping/mcp
```

Cursor 会自动走 OAuth 授权流程。

### Codex CLI

添加 MCP server 后执行：

```bash
codex mcp login dsers
```

如果客户端要求填写 URL，使用：

```text
https://mcp.dsers.com/dropshipping/mcp
```

### ChatGPT App

在 ChatGPT Developer Mode 或 Apps dashboard 中使用远程 endpoint：

```text
https://mcp.dsers.com/dropshipping/mcp
```

当前 ChatGPT 提交目标是 data-only：DSers 只暴露普通 MCP tools，不依赖 iframe widget。见 [ChatGPT App 提交说明](docs/chatgpt-app-submission.md)。

### OpenClaw

部分 OpenClaw 部署可通过 MCP registry 或社区网关连接。transport 和授权支持因运行环境而异，请以当前连接向导为准，并优先使用 OAuth 流程，不要使用 API key 或 cookie 绕过。

## 文档

- [认证说明](docs/zh-CN/auth.md)
- [工具清单](docs/zh-CN/tools.md)
- [隐私与安全](docs/zh-CN/privacy.md)
- [ChatGPT App 提交说明](docs/chatgpt-app-submission.md)
- [ChatGPT App E2E 测试链路](docs/chatgpt-app-e2e-playbook.md)
- [托管部署说明](docs/deployment.md)
- [示例](docs/zh-CN/examples.md)
- [English README](README.md)

## 示例 MCP 客户端配置

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

最小配置示例也可以查看 [`examples/remote-mcp.json`](examples/remote-mcp.json)。

## Registry 与审核文件

- [`server.json`](server.json) - 托管远程 MCP server 的 registry metadata
- [`manifest.json`](manifest.json) - 公开 app metadata、工具列表、prompt 模板和隐私政策 URL
- [`chatgpt-app-submission.json`](chatgpt-app-submission.json) - ChatGPT App 审核辅助文件，包含工具 hint 和测试 prompts

## License

本公开文档和 metadata 仓库使用 [Apache License 2.0](LICENSE)。

## Support

如需支持，请联系：

zhaohaoduo@dsers.com
