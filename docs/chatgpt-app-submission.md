# ChatGPT App Submission Notes

This repository contains the public metadata package for submitting the DSers hosted MCP server as a ChatGPT App.

## Submission Shape

- App type: data-only ChatGPT App
- MCP endpoint: `https://mcp.dsers.com/dropshipping/mcp`
- Transport: Streamable HTTP
- Authentication: OAuth 2.1 + PKCE through DSers
- Widget / iframe: not required for the current submission
- Tool surface: 13 DSers domain tools

The current submission intentionally does not rely on an iframe widget. ChatGPT receives normal MCP tool results and writes the user-facing summary in chat.

## Required Public Files

- [`server.json`](../server.json) - MCP registry metadata
- [`manifest.json`](../manifest.json) - app metadata, tool list, prompts, privacy URL, and remote endpoint
- [`chatgpt-app-submission.json`](../chatgpt-app-submission.json) - app info, tool hint justifications, positive test cases, and negative test cases
- [`docs/auth.md`](auth.md) - OAuth and endpoint documentation
- [`docs/privacy.md`](privacy.md) - hosted privacy and data handling summary
- [`docs/tools.md`](tools.md) - public tool reference
- [`docs/chatgpt-app-e2e-playbook.md`](chatgpt-app-e2e-playbook.md) - review-facing live test chain

## App Info

- Display name: DSers
- Subtitle: Import products to stores
- Category: Shopping
- Company URL: `https://www.dsers.com`
- Privacy policy URL: `https://mcp.dsers.com/privacy-policy`
- Support contact: `zhaohaoduo@dsers.com`

## Tool Hints

Every tool in `chatgpt-app-submission.json` has explicit:

- `readOnlyHint`
- `openWorldHint`
- `destructiveHint`

Important write-action classifications:

- `dsers_product_import` creates a private DSers import-list draft.
- `dsers_product_update_rules` can overwrite draft content/rules and remove draft variants through option edits, so it is marked destructive.
- `dsers_store_push` can create or publish connected-store listings after confirmation, so it is marked open-world and destructive.
- `dsers_product_delete` deletes a private import-list item after confirmation, so it is marked destructive.
- `dsers_sku_remap` preview is non-persistent, but apply mode can overwrite supplier mappings after confirmation, so the tool is marked destructive.

## OAuth Review Requirements

The review account must be able to complete OAuth without MFA, SMS, email challenge, VPN, allowlisted IP, or manual token copying. It should contain sample DSers data:

- at least one connected Shopify, Wix, or WooCommerce test store
- import-list draft data or permission to create draft-only test imports
- at least one pushed product for supplier mapping readback, if SKU remap review is required

Do not provide API keys, cookies, manual Authorization headers, or DSers passwords in the submission text.

## Review Test Coverage

Use [`chatgpt-app-submission.json`](../chatgpt-app-submission.json) for dashboard import and [`docs/chatgpt-app-e2e-playbook.md`](chatgpt-app-e2e-playbook.md) for manual replay.

The minimum test set covers:

- OAuth and store discovery
- rule validation
- inventory policy readback
- supplier search across AliExpress, Alibaba, and 1688
- private draft import and text preview
- private draft rule update and text preview
- import-list browsing
- push confirmation without immediate execution
- pushed-products readback
- alternate supplier list
- SKU remap preview
- import-list delete confirmation
- negative prompts for unrelated tasks, credentials, payments, unsafe publish, and no-preview supplier replacement

## Maintenance

For future updates, create a new draft version in the OpenAI dashboard rather than creating a new app. Update this repository and `chatgpt-app-submission.json` whenever tool names, descriptions, annotations, endpoint URLs, OAuth behavior, privacy data categories, or review test prompts change.
