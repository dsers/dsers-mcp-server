# DSers MCP 用户指南

## 1. 简介

DSers MCP 是 DSers 托管的 Streamable HTTP 服务，让经过 OAuth 授权的 AI 客户端可以处理账号、店铺、供应商商品、发布、Mapping、订单、账单与 Tracking 工作流。

本仓库只记录托管服务的公开信息，不是自托管安装包，也不包含后端源码。

## 2. 连接与授权

在支持 remote HTTP MCP 的客户端中添加 `https://ai.dsers.com/mcp`。首次连接时，点击客户端提供的 Sign in、Connect、Authorize 或 Login，并在浏览器中完成 DSers OAuth。

只配置 server URL。禁止粘贴密码、API key、cookie、店铺 token 或手工复制的 Authorization header。

## 3. 当前能力

服务提供 60 个工具，不提供 MCP Prompts 或 Resources。工具分为：

- 账号、套餐、账单与导航
- 店铺与平台设置
- 供应商选品
- 待发布商品与 Pricing Rule
- 已发布商品与 Mapping
- 发布与异步任务
- 订单与履约
- 包裹、Tracking 与供应商品变更

当前工具名和风险级别见[工具清单](tools.md)。

## 4. 使用基于证据的工作流

多数 DSers 操作需要多步完成：先读，复制精确 ID，展示拟执行效果，然后只在用户明确要求时写入。

常用证据来源：

- `list_stores`：店铺、销售渠道与供应商平台 ID
- `list_supplier_search_filters`：供应商支持的搜索条件
- `get_pre_publish_product`：当前可编辑状态与 resource version
- `get_store_product_mapping`：当前 Mapping 关系
- `list_orders` 与 `get_order`：精确订单、供应商、店铺与 tab 上下文
- `get_job_status`：异步任务最终状态

只要 DSers 工具能提供 canonical ID，就不能根据名称、URL 或已有知识推测 ID。

## 5. 确认危险操作

dangerous 工具可能发布或删除商品、修改 Mapping、编辑商品和订单、创建付款流程、取消供应商订单，或向已连接平台发送履约/通知状态。

调用前必须：

1. 读取最新资源状态。
2. 展示精确资源 ID、目标店铺或订单以及实质影响。
3. 说明不可逆或外部可见的后果。
4. 在调用前立即取得用户明确确认。

确认发生在对话层。当前工具 schema 不接受 `confirm`、`confirmation_id` 或 `idempotency_key`。

## 6. 版本与异步任务

有版本控制的资源必须传入读取工具返回的最新 resource version。遇到旧状态错误时，重新读取并展示新状态，不能强行写入。

写操作创建异步任务后，保留完整的 `task_type` 和 `task_id`，并调用 `get_job_status`。任务已创建不代表所有商品、店铺、订单或包裹已经成功完成。

## 7. 安全处理错误

工具错误是结构化 JSON，包含稳定的 `code`、message、`retryable` 和可选恢复字段。

- `UNAUTHORIZED` 或 `INSUFFICIENT_SCOPE`：重新授权。
- `PLAN_RESTRICTION`：解释套餐或 AI Credits 要求。
- `RATE_LIMITED`：遵守返回的等待时间。
- `VALIDATION_ERROR`：修正参数。
- 上游错误只有 `retryable=true` 才可重试。
- `UPSTREAM_UNKNOWN` 禁止自动重试，必须先检查资源状态。

## 8. 示例流程

供应商搜索、待发布商品修改、发布、Mapping、订单诊断和删除流程见[使用示例](examples.md)。

## 9. 隐私与支持

处理客户、订单、地址、账单或 Tracking 数据前，请阅读[隐私与安全](privacy.md)。公开 GitHub Issue 只用于非敏感的文档与连接问题；安全或账号相关问题发送到 `zhaohaoduo@dsers.com`。
