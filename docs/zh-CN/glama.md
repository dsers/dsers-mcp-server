# 通过 Glama 连接 DSers Dropshipping MCP

DSers 是托管的远程 MCP 服务。通过 Glama 连接时，无需安装本地服务器，也无需获取 DSers 后端源码。

## 平台页面

- [DSers 服务器目录条目](https://glama.ai/mcp/servers/dsers/dsers-mcp-server)
- [DSers 远程 Connector](https://glama.ai/mcp/connectors/io.github.dsers/dsers-mcp-server)

目录条目和远程 Connector 使用独立的管理与验证流程，显示名称、描述、健康检查和评估结果可能分别更新。

## 在 Glama Inspector 中连接

1. 打开 [Glama Inspector](https://glama.ai/mcp/inspector)，在工作区选择 **Add server**。
2. 名称填写 `DSers Dropshipping MCP`，服务地址填写 `https://ai.dsers.com/mcp`。
3. 认证方式选择 **OAuth 2.0**。可选的 Client ID 和 Client Secret 留空，使用自动发现和动态客户端注册。Transport 保持 **Automatic**，或选择 Streamable HTTP。
4. 添加后点击 **Connect**；提示需要授权时点击 **Authenticate**。
5. 在浏览器中完成 DSers 登录，核对 OAuth 权限并授权。返回 Inspector，确认状态为已连接。

不要在 Inspector 中填写 DSers 密码或手动复制 OAuth token。DSers 登录和授权在 DSers 自己的页面完成。

## 使用只读工具验证连接

选择 `get_account_profile`，无需填写参数即可执行。该工具只读取已授权账号的资料，不修改账号数据或权限。返回的账号资料应按私有信息处理。

2026 年 11 月 16 日验证的连接可发现 60 个工具，实际可见范围取决于服务版本、账号权限和已授予的 OAuth scopes。工具调用成功只能证明当前连接可用，不能据此认定 Glama 已更新目录评分或独立的 Connector 健康检查结果。

使用其他工具前请查看[工具清单](tools.md)。验证连接不需要发布商品、下单、创建付款链接或执行其他业务写操作。

## 理解 Connector 健康检查

未授权请求访问 DSers MCP 地址时，会返回 `401` 和 OAuth 发现信息。这是受保护端点的正常行为，不能单凭它认定服务不可用。

Glama 的远程 Connector 检查器缺少有效授权时，页面可能显示健康检查失败。Connector 的 **Test Profile** 与 Inspector 中的个人连接相互独立。Connector 所有者需要先完成认领，再通过 **Admin → Test Profile** 配置适当的测试授权，之后才能评估带认证的健康检查和工具发现结果。

不要将密码、access token、refresh token 或测试凭证发布到仓库或支持工单。如果 Test Profile 无法使用服务支持的 OAuth 流程，应先向 Glama 确认检查器支持的认证方式，再提供凭证。

连接故障请查看[认证说明](auth.md)。非敏感的文档或兼容性问题可以提交 [GitHub Issue](https://github.com/dsers/dsers-mcp-server/issues)。
