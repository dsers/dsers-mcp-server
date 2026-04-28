# Privacy and Security

The DSers Official MCP Server is hosted by DSers.

This public repository provides only public documentation, connection examples, and registry metadata. It does not contain backend source code, user credentials, access tokens, browser cookies, private API keys, or user data.

## Data Access

Depending on the user's authorization and DSers account permissions, the MCP server may access DSers workflow data such as:

- Linked store information
- Product import information
- Product editing data
- Store publishing status
- Import-list drafts
- Already pushed DSers products
- Supplier replacement workflow data
- Order-related dropshipping workflow data, if exposed by the user's DSers account and permissions

## User Authorization

DSers MCP uses OAuth 2.1 + PKCE. Users must authorize access through DSers before MCP clients can call tools.

MCP clients should not ask users to paste DSers backend API keys, store tokens, browser cookies, or passwords.

## Safety Rules

Recommended safety behavior:

- Use `backend_only` as the default `visibility_mode` for pushes.
- Do not use `sell_immediately` unless price, inventory, store, and variants have been checked.
- Do not use `force_push=true` unless the exact risk has been shown to the user and explicit confirmation has been received.
- Do not skip `dsers_sku_remap mode=preview` before applying supplier replacement.
- Deletion from the import list requires a second confirmation.

## Data Handling

User data is handled according to DSers privacy and security policies.

For privacy-related questions, contact:

zhaohaoduo@dsers.com
