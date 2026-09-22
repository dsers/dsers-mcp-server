# Examples

These examples describe tool sequences, not server-provided MCP prompts. Copy exact IDs and versions from earlier results; never invent identifiers.

## Connect a client

```json
{
  "mcpServers": {
    "dsers": {
      "type": "http",
      "url": "https://ai.dsers.com/mcp"
    }
  }
}
```

Complete OAuth through the client UI on first connection.

## Discover and inspect a supplier product

User request:

> Find wireless label printers from my available supplier apps, show two candidates, and estimate shipping to the United States. Do not import anything.

Recommended sequence:

1. `list_supplier_search_filters` to obtain available supplier-platform IDs and supported filters.
2. `search_supplier_products` with an exact returned platform ID and the user's filters.
3. `get_supplier_product` for the selected candidate.
4. `list_supplier_product_shipping_methods_and_cost` for the destination.

## Add a private pre-publish product

User request:

> Add the selected supplier product to my pre-publish list in English, then show me its current content and prices. Do not publish it.

Recommended sequence:

1. `create_pre_publish_products` with an `items` array containing the exact supplier platform and product IDs.
2. `get_pre_publish_product` with the returned import item ID.
3. Show the item and retain its current resource version for any requested edit.

## Edit pre-publish content or price

Content and price are separate workflows:

- Content, media, options, variants, SKUs, stock, and package data use `update_pre_publish_product_content`.
- Exact variant prices or a fixed price use `update_pre_publish_product_price`.
- Category, Organization, and URL handle use `get_pre_publish_product_organization` followed by `update_pre_publish_product_organization`.
- Store Pricing Rules use `get_pricing_rule`, optionally `simulate_pricing_rule`, then `update_pricing_rule`.

Before a dangerous price or content change, show the exact before/after effect and ask for explicit confirmation. Re-read after the write.

## Publish products

User request:

> Prepare publishing these two pre-publish products to Store A. Show the shipping choice and exact targets first.

Recommended sequence:

1. `list_stores` and resolve Store A to its exact `dsers_store_id`.
2. `get_pre_publish_product` for every selected item.
3. `get_push_shipping_recommendation` for the exact product-store targets.
4. Show the preflight, target stores, and selected logistics.
5. After explicit confirmation, call `publish_products_to_stores` once.
6. Poll `get_job_status` with the returned `task_type + task_id`.

Do not treat task creation as final publishing success.

## Inspect or change supplier Mapping

Recommended read-only sequence:

1. `list_managed_store_products` or `search_store_products`.
2. `get_managed_store_product`.
3. `get_store_product_mapping`.

For a manual change, prepare exact Mapping relationships and call `apply_product_mapping` only after the user confirms the final target. For AI-assisted Mapping, use `search_ai_mapping_candidates`, `start_ai_product_mapping`, poll `get_job_status`, then separately confirm and call `apply_ai_mapping`.

## Diagnose and place orders

User request:

> Show my Awaiting Order items for the selected supplier and explain what blocks placement. Do not place anything yet.

Recommended sequence:

1. `list_orders` with the exact supplier platform and status.
2. `diagnose_order_issue` using the same supplier/status evidence.
3. Use `get_order` only when rich detail or a repair is needed.
4. Apply an explicitly requested repair with the relevant order mutation tool.
5. Re-read and diagnose again.
6. After explicit confirmation, call `place_orders_to_suppliers`.
7. Poll the returned task when the result supplies `task_type + task_id`.

## Delete pre-publish products

For explicit IDs, show the exact products and ask for confirmation before `delete_pre_publish_products`. For filter-based deletion, call `list_pre_publish_products` first and freeze the exact IDs. The tool does not accept a `confirm` parameter.

## Request body examples

`create_pre_publish_products` accepts up to 10 items per call:

```json
{
  "items": [
    { "supplier_platform_id": "1", "supplier_product_id": "1005000000000000" },
    { "supplier_platform_id": "2", "supplier_product_id": "60123456789" }
  ]
}
```

`update_pre_publish_product_content` edits content, media, and variants by `import_item_id`:

```json
{
  "import_item_id": "123456",
  "supplier_product_title": "Portable Mini Blender",
  "supplier_product_description": "<p>Compact USB rechargeable blender for travel and office use.</p>",
  "delete_variants": [
    { "supplier_product_variant_id": "v3" }
  ]
}
```

`update_pre_publish_product_price` sets one fixed price, or edits exact variant rows via `supplier_product_variant_list`:

```json
{
  "import_item_id": "123456",
  "dsers_store_id": "789",
  "mode": "fixed_price",
  "fixed_price": "19.99"
}
```

## Handle failures

- `INSUFFICIENT_SCOPE`: reauthorize with `required_scopes`.
- `PLAN_RESTRICTION`: explain the returned plan or AI Credit restriction.
- `RATE_LIMITED`: wait for the returned retry window.
- `VALIDATION_ERROR`: correct arguments before retrying.
- `UPSTREAM_UNKNOWN`: do not retry the write; re-read the resource.
