# 认证说明

DSers MCP 使用 OAuth 2.1 + PKCE。客户端只需要配置远程 MCP URL：

```text
https://ai.dsers.com/mcp
```

不要配置 API key，不要在客户端之间复制 access token，也不要手工添加 `Authorization` header。

## 发现与授权

受保护资源在以下地址发布 RFC 9728 metadata：

```text
GET https://ai.dsers.com/.well-known/oauth-protected-resource
```

未认证的 MCP 请求会返回 `401`、包含 Protected Resource Metadata 地址的 `WWW-Authenticate` header，以及引导客户端开始 OAuth 的机器可读响应。

Metadata 会说明 authorization server 和当前已注册工具使用的 OAuth scopes。兼容的 MCP 客户端应发现 authorization server、打开 DSers 授权页、完成 authorization-code + PKCE，然后在 MCP 请求中发送签发的 Bearer token。

## Roles 与 scopes

授权分两层执行：

1. HTTP 入口通过配置的 DSers OAuth 验证服务校验 Bearer token。
2. 工具列表和工具调用继续校验每个工具允许的 role 与所需 OAuth scopes。

当前工具支持 `admin`、`dsers`、`mcp` 三类 role。外部 OAuth 调用还必须具备目标工具所需的 scopes；部分工具会根据本次参数收窄实际 scope 集合。

scope 不足时，工具返回带 `required_scopes` 的 `INSUFFICIENT_SCOPE`。此时应重新授权 MCP 连接，不能伪造 token 或用相同凭证反复重试。

## 常见失败

- `401 UNAUTHORIZED`：在客户端界面连接或重新授权。
- `403 FORBIDDEN`：当前账号或 role 无权使用目标工具或资源。
- `INSUFFICIENT_SCOPE`：按返回的 scopes 重新授权。
- `503 service_unavailable`：验证服务暂时不可用，应稍后重试，而不是强制用户重新登录。

## 凭证安全

不要在 prompt、工具参数、GitHub Issue 或截图中放入 DSers 密码、MFA code、后台 API key、店铺 token、浏览器 cookie、OAuth code、access token 或 refresh token。每个 MCP 客户端应独立管理自己的 OAuth session。
