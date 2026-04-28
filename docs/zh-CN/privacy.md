# 隐私与安全

DSers Official MCP Server 由 DSers 托管。

本公开仓库只提供公开文档、连接示例和 registry metadata，不包含后端源码、用户凭证、access token、浏览器 cookie、私有 API key 或用户数据。

## 数据访问范围

根据用户授权和 DSers 账号权限，MCP server 可能访问 DSers 工作流数据，例如：

- 已连接店铺信息
- 商品导入信息
- 商品编辑数据
- 店铺发布状态
- Import-list drafts
- 已推送的 DSers 商品
- 供应商替换工作流数据
- 如果用户账号权限允许，也可能访问订单相关 dropshipping 工作流数据

## 用户授权

DSers MCP 使用 OAuth 2.1 + PKCE。用户必须先通过 DSers 授权，MCP 客户端才能调用工具。

MCP 客户端不应要求用户粘贴 DSers 后台 API key、店铺 token、浏览器 cookie 或密码。

## 安全规则

建议遵循以下安全行为：

- 默认 push 使用 `backend_only`。
- 使用 `sell_immediately` 前必须确认价格、库存、店铺和 variants。
- 只有在明确展示风险并获得用户确认后，才允许使用 `force_push=true`。
- 供应商替换前不要跳过 `dsers_sku_remap mode=preview`。
- 删除 import list 中的 draft 必须二次确认。

## 数据处理

用户数据根据 DSers 隐私和安全政策处理。

如有隐私相关问题，请联系：

zhaohaoduo@dsers.com
