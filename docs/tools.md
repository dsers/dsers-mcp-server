# Tools

The DSers Official MCP Server provides tools for AI-powered dropshipping workflows.

## Recommended First Call

### `dsers_store_discover`

Returns linked stores and supported rule capabilities. Call this first in most workflows.

Common input:

- `target_store`: optional store ID or name filter

Key output:

- `store_discovery.state`
- `stores[].id`
- `stores[].name`
- `stores[].platform`
- `stores[].currency`
- `stores[].ship`
- `rules.pricing`
- `rules.content`
- `rules.images`
- `plan_issue`

`store_discovery.state` helps clients distinguish connected stores from missing stores, authorization problems, upstream failures, unrecognized response shapes, and target-store misses. Successful discovery may also include `mcp_update` guidance when the client should refresh, reauthorize, or reinstall the MCP connection.

## Rule Validation

### `dsers_rules_validate`

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

## Product Search and Import

### `dsers_find_product`

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

### `dsers_product_import`

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

## Draft Preview and Updates

### `dsers_product_preview`

Reloads a DSers import draft by `import_item_id`.

Common input:

- `import_item_id`
- `variant_detail`: `compact` or `full`
- `variant_offset`
- `variant_limit`
- `show_all_options`
- `include_images`

Usage notes:

- `compact` is best for quickly listing all SKUs.
- Use `full` when cost, compare-at price, or supplier quantity is needed.
- Use `show_all_options=true` before editing options.
- Use `include_images=true` when matching variants visually.

### `dsers_product_update_rules`

Updates pricing, content, image, or variant rules on an already imported draft.

Common input:

- `import_item_id`
- `rules_json`
- `target_store`
- `visibility_mode`

Merge behavior:

- `pricing`, `images`, and `variant_overrides` replace their rule family.
- `title_prefix`, `title_suffix`, and `description_append_html` are slots; each new value replaces the old slot value.
- `option_edits` is a full replacement, not an incremental merge.
- `remove_value` option edits remove the matching draft variants.
- Use `{"pricing": null}` to remove a whole rule family.

If persisting rules to DSers fails, the tool returns an error to prevent later pushes from using stale draft values.

## Lists and Published Products

### `dsers_import_list`

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

### `dsers_my_products`

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

## Publishing

### `dsers_store_push`

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

## Deletion

### `dsers_product_delete`

Deletes an item from the DSers import list. This is irreversible.

Common input:

- `import_item_id`
- `confirm`

Protocol:

1. First call without `confirm=true`.
2. Show the confirmation response to the user.
3. Call again with `confirm=true` only after explicit approval.

This does not delete already published storefront listings.

## Inventory Policy

### `dsers_inventory_policy_get`

Reads the DSers account inventory sync policy.

Key output:

- supplier product unavailable behavior
- single variant unavailable behavior
- auto-sync stock behavior
- plain-language labels for each setting

This is read-only and does not change account settings.

## Supplier Replacement

### `dsers_alt_supplier_list`

Lists the primary and alternate suppliers mapped to an existing DSers product.

Common input:

- `dsers_product_id`
- `variant_detail`

Key output:

- supplier URL
- supplier platform and app ID
- product title and image
- variant count
- supplier-native currency and currency source

Use this before choosing a supplier URL for `dsers_sku_remap`.

### `dsers_sku_remap`

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

1. Call with `mode=preview`.
2. Review `diffs`, `summary`, `top_candidates`, and `warnings`.
3. Confirm the mapping.
4. Call again with `mode=apply`.

Do not skip preview.

## Prompt Templates

The server also exposes four prompt/workflow templates:

| Name | Purpose |
| --- | --- |
| `dsers_workflow_quick-import` | Import one product and push it as a draft |
| `dsers_workflow_bulk-import` | Batch import products with a pricing multiplier |
| `dsers_workflow_multi-push` | Push one product to all connected Shopify or Wix stores |
| `dsers_workflow_seo-optimize` | Import, rewrite title/description with the LLM, then push |

These are optional client-side workflow shortcuts, not required tool calls.
