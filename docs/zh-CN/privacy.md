# 隐私与安全

DSers Official MCP Server 由 DSers 托管。这个 public 仓库只包含文档和 metadata，不包含生产凭证或客户数据。

托管隐私政策：

```text
https://mcp.dsers.com/privacy-policy
```

## 处理的数据

根据授权账号、所选工具和调用参数，服务可能处理：

- OAuth 身份、roles、scopes、DSers 用户 ID，以及调用 DSers 服务所需的短生命周期请求凭证。
- 账号、套餐、AI Credits、账单、发票、店铺、应用与平台设置数据。
- 供应商商品、待发布商品、已管理店铺商品、变体、图片、价格、库存、Mapping 与发布数据。
- 订单、地址、账单记录、供应商订单、物流方式、包裹、Tracking Number 与通知状态。
- 工具名、状态、耗时、request ID、rate limit、额度消耗和审计 metadata 等运行数据。

## 存储与日志

MCP BFF 的协议 session 不依赖本地状态，但当前服务会在确有需要时持久化部分运行与工作流数据：

- 工具审计记录可能写入 DSers MySQL 基础设施；
- 异步任务状态可能被保存，以支持按账号查询 Job；
- Redis 可能用于额度、频率限制、锁与短期缓存；
- 日志、指标和 trace 可能包含最小化的运行 metadata。

通用审计序列化会脱敏 token、secret、街道地址、联系人、电话、邮箱、税务、公司和护照字段。地址相关工具使用摘要级审计策略。

具体保留期限由已部署的 DSers 服务和运行政策决定。本仓库不承诺固定的 token、审计、analytics、缓存或任务保留时间。

## 连接的服务

MCP 服务会与 DSers 的账号、套餐、商品、设置、订单、Tracking 等服务通信。用户要求的操作也可能影响已连接的销售平台或供应商应用，例如 Shopify 发布、履约或买家物流通知。

## 安全规则

- 使用 OAuth，禁止提交密码、MFA code、API key、cookie 或手工复制的 token。
- 修改前先读取当前状态，并使用精确 DSers ID。
- dangerous 工具调用前必须立即取得用户明确确认。
- `UPSTREAM_UNKNOWN` 写请求禁止自动重试。
- 后续修改前重新读取有版本控制的资源，并使用最新 resource version。

## 联系方式

隐私或安全问题请联系 `zhaohaoduo@dsers.com`。
