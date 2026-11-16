# Connect DSers Dropshipping MCP with Glama

DSers is a hosted, remote MCP service. You can connect from Glama without installing a local server or accessing the DSers backend source code.

## Listings

- [DSers server listing](https://glama.ai/mcp/servers/dsers/dsers-mcp-server)
- [DSers remote connector](https://glama.ai/mcp/connectors/io.github.dsers/dsers-mcp-server)

The server listing and remote connector have separate administration and verification. Their displayed names, descriptions, health checks, and evaluation results can update independently.

## Connect in Glama Inspector

1. Open [Glama Inspector](https://glama.ai/mcp/inspector) and choose **Add server** in your workspace.
2. Set the server name to `DSers Dropshipping MCP` and the server URL to `https://ai.dsers.com/mcp`.
3. Select **OAuth 2.0** for authentication. Leave the optional client ID and client secret empty to use discovery and dynamic client registration. Transport can remain **Automatic**, or use Streamable HTTP.
4. Add the server, choose **Connect**, and then **Authenticate** when prompted.
5. Complete DSers login and review the OAuth permissions in the browser. Return to Inspector and check that the server is connected.

Do not enter a DSers password into Inspector or manually copy OAuth tokens into the connection. DSers authentication takes place on the DSers login and authorization pages.

## Verify the connection with a read-only tool

Choose `get_account_profile` and execute it without arguments. This reads the authorized account's profile and does not change account data or permissions. Treat the returned profile as private account information.

The verified connection exposed 60 tools on November 16, 2026. The tools visible to your connection depend on the service version, your account permissions, and granted OAuth scopes. A successful tool call confirms that connection works; it does not establish that Glama has updated a directory score or a separate connector health check.

See the [tool catalog](tools.md) before running another tool. Product publishing, order placement, payment-link creation, and other business writes are not needed to verify the connection.

## Understand connector health checks

An unauthenticated request to the DSers MCP endpoint returns `401` with OAuth discovery information. This is expected for a protected endpoint and does not by itself show that the service is unavailable.

Glama's remote connector page can report failed health checks when its checker lacks valid authentication. A connector **Test Profile** is separate from a personal connection in Inspector. The connector owner must claim the connector and configure an appropriate test profile under **Admin → Test Profile** before authenticated health checks and tool discovery can be assessed.

Do not publish passwords, access tokens, refresh tokens, or test credentials in this repository or a support issue. If a test profile cannot use the supported OAuth flow, ask Glama which authentication method its checker supports before providing credentials.

For connection failures, see [Authentication](auth.md). For non-sensitive documentation or compatibility questions, open a [GitHub issue](https://github.com/dsers/dsers-mcp-server/issues).
