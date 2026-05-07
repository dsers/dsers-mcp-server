# Privacy and Security

The DSers Official MCP Server is hosted by DSers. This public repository provides documentation, connection examples, and registry metadata. It does not contain backend source code, user credentials, access tokens, browser cookies, private API keys, or user data.

Hosted privacy policy URL:

```text
https://mcp.dsers.com/privacy-policy
```

## Data Processed by the Hosted Service

Depending on the user's authorization and DSers account permissions, the MCP server may process:

- OAuth authorization data: DSers OAuth tokens received through the authorization flow and wrapped into encrypted MCP access and refresh tokens.
- DSers account and store data: store IDs, store names, platform type, currencies, shipping settings, pricing-rule capabilities, and related account metadata.
- Product and supplier data: product titles, images, prices, variants, SKU mappings, stock status, supplier URLs, import item IDs, DSers product IDs, push status, and rule settings.
- Operational metadata: request and tool names, success or failure status, coarse client type, coarse country code when provided by the hosting platform, timestamps, correlation identifiers, instance identifiers, and DSers user identifiers used for diagnostics, abuse prevention, and aggregate usage reporting.

The service should not request DSers passwords, MFA codes, API keys, browser cookies, payment card data, government identifiers, or health information through MCP tool inputs.

## User Authorization

DSers MCP uses OAuth 2.1 + PKCE. Users must authorize access through DSers before MCP clients can call tools.

MCP clients should not ask users to paste DSers backend API keys, store tokens, browser cookies, or passwords. If authorization fails, reconnect the MCP server or re-run the OAuth login flow.

## Storage and Retention

The hosted service is designed to be stateless for user workflow data. Product and store data is processed during requests and is not stored in a product database operated by this MCP service.

- Authorization codes expire after 10 minutes.
- Wrapper access tokens are short lived and capped by the underlying DSers OAuth token lifetime and server maximum lifetime.
- Wrapper refresh tokens can last up to 30 days so compatible MCP clients can refresh sessions.
- Optional aggregate daily analytics keys expire after 90 days, hourly analytics keys expire after 14 days, and total counters may be retained until deleted by the operator.

## Third Parties

The hosted service communicates with:

- DSers OAuth and API services, to authenticate the user and perform requested DSers actions.
- Connected store platforms such as Shopify, Wix, or WooCommerce, when the user asks DSers to push a product to a connected store.
- Supplier and product sources such as AliExpress, Alibaba, Accio, 1688, and related image or product sources when the user asks to search, import, preview, or match products.
- Upstash Redis, only when analytics environment variables are configured for aggregate operational counters.

The service does not sell personal data and does not use advertising networks.

## Safety Rules

Recommended safety behavior:

- Use `backend_only` as the default `visibility_mode` for pushes.
- Do not use `sell_immediately` unless price, inventory, store, and variants have been checked.
- Do not use `force_push=true` unless the exact risk has been shown to the user and explicit confirmation has been received.
- Do not skip `dsers_sku_remap mode=preview` before applying supplier replacement.
- Deletion from the import list requires a second confirmation.

## Contact

For privacy-related questions, contact:

zhaohaoduo@dsers.com
