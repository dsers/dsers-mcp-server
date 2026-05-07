# DSers MCP User Guide

## 1. Overview

DSers MCP is a hosted Model Context Protocol server:

```text
https://mcp.dsers.com/dropshipping/mcp
```

It exposes DSers dropshipping workflows to MCP clients that support remote HTTP transport and OAuth 2.1 + PKCE.

Typical use cases:

- Import products from AliExpress, Alibaba, 1688, or Accio into the DSers import list
- Apply pricing, title, description, image, and variant rules
- Preview DSers drafts before pushing to Shopify or Wix
- Search the DSers product pool
- Browse import-list drafts and already pushed products
- Replace suppliers on existing store products with SKU-level matching

## 2. Requirements

You need:

- A DSers account
- At least one Shopify or Wix store connected in DSers
- An MCP client with remote HTTP MCP and OAuth support

Verified or expected clients:

| Client | Status | Notes |
| --- | --- | --- |
| Claude Desktop | Works | Configure as a remote HTTP MCP server |
| Cursor | Works | Settings → Tools & Integrations → MCP Tools |
| Claude Code | Works | `claude mcp add` |
| Codex CLI | Works | `codex mcp login` |
| VS Code / Cline / Windsurf / Zed / Continue | Expected | Requires OAuth-capable MCP support |
| OpenClaw | Works | Can connect through the OpenClaw MCP registry or community MCP directories such as AgentHotspot / ClawHub |

Client selection rules:

- This MCP is a remote HTTP service. It does not provide a local stdio server.
- The client must support OAuth 2.1 + PKCE for remote MCP.
- If a client's remote OAuth MCP support is incomplete, use Claude Desktop, Claude Code, Cursor, Codex CLI, or an OpenClaw community MCP directory/gateway.
- OpenClaw is highly configurable and works well with community MCP resources or custom MCP configuration. The exact transport support depends on the current OpenClaw runtime.

## 3. Initial Setup

### 3.1 Claude Desktop

Add this to `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "dsers": {
      "type": "http",
      "url": "https://mcp.dsers.com/dropshipping/mcp"
    }
  }
}
```

Restart Claude Desktop. On first tool use, the client starts OAuth and opens the DSers login page. Sign in and authorize.

### 3.2 Claude Code

```bash
claude mcp add dsers https://mcp.dsers.com/dropshipping/mcp --transport http
```

Then authenticate:

```bash
claude mcp login dsers
```

### 3.3 Cursor

Open:

```text
Settings → Tools & Integrations → MCP Tools → Add / Connect
```

Use this server URL:

```text
https://mcp.dsers.com/dropshipping/mcp
```

Cursor should start the OAuth flow automatically.

### 3.4 Codex CLI

After adding the MCP server, run:

```bash
codex mcp login dsers
```

If the client asks for the server URL, use:

```text
https://mcp.dsers.com/dropshipping/mcp
```

### 3.5 OpenClaw

OpenClaw can save remote MCP server definitions through its MCP registry. For self-hosted or CLI setups:

```bash
openclaw mcp set dsers '{"url":"https://mcp.dsers.com/dropshipping/mcp","transport":"streamable-http"}'
```

You can also add DSers MCP through community MCP directories such as AgentHotspot or ClawHub. Transport and authorization support can vary by OpenClaw deployment, so follow the connection guide for your current environment.

## 4. Authentication Model

DSers MCP uses OAuth 2.1 + PKCE. Do not configure API keys. Do not manually add an `Authorization` header.

Flow:

1. Add the DSers MCP server URL in your MCP client
2. On first tool use, the client opens the authorization page
3. Sign in with your DSers account and approve access
4. Return to the MCP client and start calling tools

Important details:

- If authorization fails, reconnect DSers MCP or re-run the login flow in your MCP client
- Do not paste DSers backend API keys, store tokens, or browser cookies into the MCP client
- For team environments, use a dedicated DSers account instead of sharing a personal account

## 5. Recommended Workflows

### 5.1 Import One Product and Push as Draft

1. `dsers_store_discover`
2. `dsers_product_import`
3. `dsers_product_preview`
4. Confirm title, price, inventory, and variants
5. `dsers_store_push` with `visibility_mode=backend_only`

### 5.2 Import with Pricing Rules

```json
{
  "pricing": {
    "mode": "multiplier",
    "multiplier": 2
  }
}
```

Recommended sequence:

1. `dsers_store_discover`
2. `dsers_rules_validate`
3. `dsers_product_import` with `rules_json`
4. `dsers_product_preview`
5. `dsers_store_push`

### 5.3 Batch Import

Use `source_urls_json` in `dsers_product_import`:

```json
[
  "https://www.aliexpress.com/item/1005000000000000.html",
  {
    "url": "https://www.alibaba.com/product-detail/example.html",
    "rules": {
      "pricing": {
        "mode": "multiplier",
        "multiplier": 2.5
      }
    }
  }
]
```

Batch imports return summary output by default. Use `batch_detail=full` only when full previews are required.

### 5.4 Replace a Supplier on an Existing Product

1. `dsers_store_discover` to get `store_id`
2. `dsers_my_products` to get `dsers_product_id`
3. `dsers_sku_remap` with `mode=preview`
4. Review the variant-level matching plan
5. Call `dsers_sku_remap` again with `mode=apply`

## 6. Tool Reference

### dsers_store_discover

Returns linked stores and supported rule capabilities. Call this first in most workflows.

Common input:

- `target_store`: optional store ID or name filter

Key output:

- `stores[].id`
- `stores[].name`
- `stores[].platform`
- `stores[].ship`
- `rules.pricing`
- `rules.content`
- `rules.images`
- `plan_issue`

### dsers_rules_validate

Validates a rule object before import or update. Use it for pricing, content, image, and variant rules.

Common input:

- `rules`: JSON string
- `target_store`: optional

Example:

```json
{
  "pricing": {
    "mode": "fixed_markup",
    "fixed_markup": 5
  },
  "content": {
    "title_prefix": "[US] "
  },
  "images": {
    "keep_first_n": 5
  }
}
```

### dsers_find_product

Searches the DSers product pool by keyword or image URL.

Common input:

- `keyword`
- `image_url`: visual search; takes priority over `keyword`
- `supplier`: `aliexpress`, `alibaba`, or `ali1688`
- `ship_to`: destination country, default `US`
- `ship_from`: origin country
- `sort`: `relevance`, `newest`, or `price`
- `limit`: max 50
- `search_after`: pagination cursor

Use the returned `import_url` directly with `dsers_product_import`.

### dsers_product_import

Imports supplier products into the DSers import list and returns a preview.

Common input:

- `source_url`: single product URL
- `source_urls_json`: batch JSON array
- `source_hint`: `auto`, `aliexpress`, `alibaba`, or `accio`
- `country`: country code, default `US`
- `target_store`: store ID or name
- `visibility_mode`: `backend_only` or `sell_immediately`
- `rules_json`: JSON string
- `batch_detail`: `summary` or `full`

Flat rule inputs are also supported:

- `pricing_mode`
- `pricing_multiplier`
- `pricing_fixed_markup`
- `pricing_fixed_price`
- `title_override`
- `title_prefix`
- `title_suffix`
- `description_override_html`
- `description_append_html`

Key output:

- `import_item_id`
- `title`
- `price_summary`
- `variant_count`
- `active_rules`

`import_item_id` is the persistent handle for preview, update, push, and delete.

### dsers_product_preview

Reloads a DSers import draft by `import_item_id`.

Common input:

- `import_item_id`
- `variant_detail`: `compact` or `full`
- `variant_offset`
- `variant_limit`
- `show_all_options`
- `include_images`

Usage notes:

- `compact` is best for quickly listing all SKUs
- Use `full` when cost, compare-at price, or supplier quantity is needed
- Use `show_all_options=true` before editing options
- Use `include_images=true` when matching variants visually

### dsers_product_update_rules

Updates pricing, content, image, or variant rules on an already imported draft.

Common input:

- `import_item_id`
- `rules_json`
- `target_store`
- `visibility_mode`

Merge behavior:

- `pricing`, `images`, and `variant_overrides` replace their rule family
- `title_prefix`, `title_suffix`, and `description_append_html` are slots; each new value replaces the old slot value
- `option_edits` is a full replacement, not an incremental merge
- Use `{"pricing": null}` to remove a whole rule family

If persisting rules to DSers fails, the tool returns an error to prevent later pushes from using stale draft values.

### dsers_import_list

Lists DSers import-list drafts.

Common input:

- `page`
- `page_size`

Key output:

- `import_item_id`
- `title`
- `sell_price_range`
- `cost_range`
- `variant_count`
- `total_stock`
- `push_status`
- `source_url`

### dsers_my_products

Lists products already pushed to a specific store.

Common input:

- `store_id`: from `dsers_store_discover`
- `page`
- `page_size`

Key output:

- `dsers_product_id`
- `title`
- `sell_price`
- `cost`
- `status`
- `supplier_url`

`dsers_product_id` is required for `dsers_sku_remap`.

### dsers_store_push

Pushes import drafts to Shopify or Wix.

Common input:

- `import_item_id`
- `import_item_ids_json`
- `target_store`
- `target_stores_json`
- `visibility_mode`
- `push_options_json`
- `force_push`

Recommended default:

```json
{
  "visibility_mode": "backend_only"
}
```

Safety checks:

- Blocks below-cost pricing
- Blocks zero price
- Blocks all-zero variant stock
- Blocks empty variants
- Warns on low margin, low stock, or very low price

Use `force_push=true` only after showing the exact risk to the user and receiving explicit confirmation.

### dsers_product_delete

Deletes an item from the DSers import list. This is irreversible.

Common input:

- `import_item_id`
- `confirm`

Protocol:

1. First call without `confirm=true`
2. Show the confirmation response to the user
3. Call again with `confirm=true` only after explicit approval

This does not delete already published storefront listings.

### dsers_sku_remap

Replaces the supplier for an already pushed store product with SKU-level matching.

Modes:

- Strict: provide `new_supplier_url`
- Discover: omit `new_supplier_url`; the tool reverse-image-searches candidates

Common input:

- `dsers_product_id`: from `dsers_my_products`
- `store_id`: from `dsers_store_discover`
- `new_supplier_url`: optional
- `mode`: `preview` or `apply`
- `country`: default `US`
- `auto_confidence`: default 70
- `max_candidates`: Discover mode candidate count

Correct sequence:

1. Call with `mode=preview`
2. Review `diffs`, `summary`, `top_candidates`, and `warnings`
3. Confirm the mapping
4. Call again with `mode=apply`

Do not skip preview.

## 7. Prompt Templates

The server also exposes four prompt/workflow templates:

| Name | Purpose |
| --- | --- |
| `dsers_workflow_quick-import` | Import one product and push it as a draft |
| `dsers_workflow_bulk-import` | Batch import products with a pricing multiplier |
| `dsers_workflow_multi-push` | Push one product to all linked stores |
| `dsers_workflow_seo-optimize` | Import, rewrite title/description with the LLM, then push |

These are optional client-side workflow shortcuts, not required tool calls.

## 8. Rules JSON Cheat Sheet

### Pricing

```json
{
  "pricing": {
    "mode": "multiplier",
    "multiplier": 2
  }
}
```

```json
{
  "pricing": {
    "mode": "fixed_markup",
    "fixed_markup": 5
  }
}
```

```json
{
  "pricing": {
    "mode": "fixed_price",
    "fixed_price": 19.99
  }
}
```

### Content

```json
{
  "content": {
    "title_override": "Portable Mini Blender",
    "description_override_html": "<p>Compact USB rechargeable blender for travel and office use.</p>",
    "tags_add": ["kitchen", "portable"]
  }
}
```

### Images

```json
{
  "images": {
    "keep_first_n": 5,
    "drop_indexes": [0, 3]
  }
}
```

### Variant Overrides

```json
{
  "variant_overrides": [
    {
      "match": "Red",
      "sell_price": 19.99,
      "compare_at_price": 29.99
    }
  ]
}
```

### Option Edits

```json
{
  "option_edits": [
    {
      "action": "rename_option",
      "option_name": "Color",
      "new_name": "Style"
    },
    {
      "action": "remove_value",
      "option_name": "Color",
      "value_name": "Gray"
    }
  ]
}
```

## 9. Common Error Handling

| Symptom | Action |
| --- | --- |
| Client reports unauthorized | Re-run MCP OAuth login; do not paste an API key |
| Still cannot call tools after authorization | Remove and re-add the MCP server, then authorize again |
| Push is blocked | Read the blocked reason and fix price, stock, or variants |
| `import_item_id` is unknown | Call `dsers_import_list` to re-check the draft |
| `store_id` is unknown | Call `dsers_store_discover` |
| Supplier replacement is uncertain | Use `dsers_sku_remap mode=preview`; do not apply |

## 10. Developer Notes

- Public prices are displayed in USD
- `store_id` and `dsers_product_id` should be strings, not JavaScript numbers
- `import_item_id` identifies an import-list draft
- `dsers_product_id` identifies an already pushed store product
- Default pushes should use `backend_only`
- `sell_immediately` requires checking price, inventory, store, and variants first
- Import-list deletion requires a second confirmation
- `force_push` requires explicit user acknowledgement of the risk
