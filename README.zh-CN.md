# DSers Official MCP Server

DSers 官方托管的 Model Context Protocol 服务，让 AI 客户端可以操作 DSers 工作流。

这个 public 仓库提供 DSers 托管服务的连接信息、用户文档、示例和 Registry metadata，**不包含生产后端源码**。

## Remote MCP Endpoint

```text
https://ai.dsers.com/mcp
```

服务使用 Streamable HTTP，不提供本地 stdio server。

## 认证

DSers MCP 使用 OAuth 2.1 + PKCE。MCP 客户端只需要配置 server URL。不要把 DSers 密码、API key、店铺 token、浏览器 cookie 或手工复制的 `Authorization` header 写入客户端配置。

Protected Resource Metadata 地址：

```text
https://ai.dsers.com/.well-known/oauth-protected-resource
```

授权后，服务会根据登录身份的 role 和 OAuth scopes 过滤并校验可用工具。详见[认证说明](docs/zh-CN/auth.md)。

## 当前能力

当前服务提供 60 个工具，覆盖：

- 账号资料、套餐、AI Credits、账单与发票
- 已连接店铺、店铺连接、重新授权与 Shopify Shipping Profile
- 供应商筛选、商品搜索、商品详情与运费查询
- 待发布商品、内容、价格、分类、Organization 与 Pricing Rule
- 已发布商品、供应商 Mapping 与 AI Mapping
- 商品发布与异步任务状态查询
- 订单搜索、诊断、修改、下单、付款、取消与履约
- 包裹、Tracking Number、买家通知与供应商品变更通知

服务只声明 MCP Tools，不提供 MCP Prompts、Resources 或 iframe widget。

完整清单见[工具文档](docs/zh-CN/tools.md)。

## 快速开始

### 通用 MCP 配置

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

首次连接时，在 MCP 客户端中点击 Sign in、Connect、Authorize 或 Login，并在浏览器中完成 DSers 授权。

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

在 ChatGPT Developer Mode 或 Apps dashboard 中添加 `https://ai.dsers.com/mcp`。当前集成是 data-only，返回普通 MCP tool result，不使用 iframe widget。

## 安全模型

工具分为 read、write、dangerous 三类。状态变更流程使用精确资源 ID、归属校验、OAuth scopes、必要的旧状态校验，并要求在危险调用前取得用户明确确认。如果写请求发出后无法确认上游结果，服务会返回不可自动重试的错误；客户端应先重新读取当前状态。

工具使用结构化错误码，帮助客户端区分重新授权、scope 不足、套餐限制、频率限制、参数错误以及结果未知的上游写入。

## 文档

- [认证说明](docs/zh-CN/auth.md)
- [工具清单](docs/zh-CN/tools.md)
- [用户指南](docs/zh-CN/user-guide.zh-CN.md)
- [使用示例](docs/zh-CN/examples.md)
- [隐私与安全](docs/zh-CN/privacy.md)
- [ChatGPT App 提交说明](docs/chatgpt-app-submission.md)
- [ChatGPT App E2E 测试](docs/chatgpt-app-e2e-playbook.md)
- [托管服务说明](docs/deployment.md)
- [English README](README.md)

## Registry 与审核文件

- [`server.json`](server.json) — MCP Registry metadata
- [`manifest.json`](manifest.json) — 公开 app metadata 和工具清单
- [`chatgpt-app-submission.json`](chatgpt-app-submission.json) — ChatGPT App 审核 metadata 与测试用例

## License

本仓库的公开文档与 metadata 使用 [Apache License 2.0](LICENSE)。

## Support

普通文档、连接、客户端兼容性或使用问题，请提交 [GitHub Issue](https://github.com/dsers/dsers-mcp-server/issues)。

漏洞、token 或账号敏感数据问题请联系 `zhaohaoduo@dsers.com`。
