# 认证说明

DSers MCP 使用 OAuth 2.1 + PKCE。

不要手动配置 API key。  
不要手动写 `Authorization` header。  
不要把 DSers 后台 API key、店铺 token 或浏览器 cookie 粘贴到 MCP 客户端。

## Remote Endpoint

```text
https://mcp.dsers.com/dropshipping/mcp
```

## 认证流程

1. 在 MCP 客户端中添加 DSers MCP server URL。
2. 首次调用工具时，客户端打开 DSers 授权页面。
3. 使用 DSers 账号登录并完成授权。
4. 回到 MCP 客户端后即可调用工具。

## 前置条件

- 一个可用的 DSers 账号
- DSers 已连接至少一个 Shopify 或 Wix 店铺
- 一个支持远程 HTTP MCP 和 OAuth 2.1 + PKCE 的 MCP 客户端

## 注意事项

- 授权失败时，优先在 MCP 客户端里重新登录或重新连接 DSers MCP。
- 团队环境建议使用独立 DSers 账号授权，避免多人共享个人账号。
- 如果怀疑账号会话泄露，请及时撤销授权。
- 本公开仓库不存储用户凭证、access token、浏览器 cookie 或后端源码。
