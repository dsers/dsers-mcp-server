# 隐私与安全

DSers Official MCP Server 由 DSers 托管。本公开仓库只提供公开文档、连接示例和 registry metadata，不包含后端源码、用户凭证、access token、浏览器 cookie、私有 API key 或用户数据。

托管隐私政策 URL：

```text
https://mcp.dsers.com/privacy-policy
```

## 托管服务处理的数据

根据用户授权和 DSers 账号权限，MCP server 可能处理：

- OAuth 授权数据：通过授权流程收到的 DSers OAuth token，并包装成加密的 MCP access / refresh token。
- DSers 账号和店铺数据：店铺 ID、店铺名称、平台类型、币种、物流设置、定价规则能力和相关账号 metadata。
- 商品和供应商数据：商品标题、图片、价格、variants、SKU mapping、库存状态、供应商 URL、import item ID、DSers product ID、push 状态和规则设置。
- 运行 metadata：请求和工具名称、成功/失败状态、粗粒度客户端类型、平台提供的粗粒度国家码、时间戳、correlation identifier、instance identifier，以及用于诊断、滥用防护和聚合统计的 DSers 用户标识。

MCP 工具输入不应要求用户提供 DSers 密码、MFA code、API key、浏览器 cookie、银行卡数据、政府证件号或健康信息。

## 用户授权

DSers MCP 使用 OAuth 2.1 + PKCE。用户必须先通过 DSers 授权，MCP 客户端才能调用工具。

MCP 客户端不应要求用户粘贴 DSers 后台 API key、店铺 token、浏览器 cookie 或密码。授权失败时，应重新连接 MCP server 或重新走 OAuth 登录流程。

## 存储与保留

托管服务对用户工作流数据采用无状态设计。商品和店铺数据只在请求过程中处理，不会存入本 MCP 服务运营的商品数据库。

- Authorization code 10 分钟过期。
- Wrapper access token 是短生命周期，并受 DSers OAuth token 生命周期和服务端最大生命周期限制。
- Wrapper refresh token 最长可保留 30 天，便于兼容客户端刷新会话。
- 可选聚合统计中，daily analytics key 90 天过期，hourly analytics key 14 天过期，total counter 可保留至 operator 删除。

## 第三方服务

托管服务会与以下服务通信：

- DSers OAuth 和 API 服务，用于认证用户并执行用户请求的 DSers 操作。
- 已连接店铺平台，例如 Shopify 或 Wix；当用户要求通过 DSers 推送商品到已连接店铺时会涉及这些平台。
- 供应商和商品来源，例如 AliExpress、Alibaba、Accio、1688，以及相关图片或商品来源；用于搜索、导入、预览或匹配商品。
- Upstash Redis，仅在部署配置了 analytics 环境变量时用于聚合运行计数。

本服务不出售个人数据，也不使用广告网络。

## 安全规则

建议遵循以下安全行为：

- 默认 push 使用 `backend_only`。
- 使用 `sell_immediately` 前必须确认价格、库存、店铺和 variants。
- 只有在明确展示风险并获得用户确认后，才允许使用 `force_push=true`。
- 供应商替换前不要跳过 `dsers_sku_remap mode=preview`。
- 删除 import list 中的 draft 必须二次确认。

## 联系方式

如有隐私相关问题，请联系：

zhaohaoduo@dsers.com
