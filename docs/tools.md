# Tools

The DSers Official MCP Server currently exposes 60 tools. The server advertises tools only; it does not expose MCP prompts or resources.

Tool availability is filtered by the authenticated role and OAuth scopes. All registered tools support the `admin`, `dsers`, and `mcp` roles. Most tools require one or more scopes, while `find_feature_entry` intentionally accesses no protected business resource.

## Account, plans, billing, and navigation

| Tool | Risk | Purpose |
|---|---|---|
| `get_account_profile` | read | Read the authenticated account profile, timezone, permissions, and subaccounts. |
| `get_plan_and_credits` | read | Read active subscriptions, plan benefits, and current AI Credit balances. |
| `get_plan_and_credit_billing` | read | Read subscription purchases or AI Credit usage, or obtain an invoice download URL. |
| `get_order_paymentlink_and_billing` | read | Search account-owned order billing records. |
| `recommend_apps_to_install` | read | Discover visible seller and supplier apps that are not installed. It never installs an app. |
| `find_feature_entry` | read | Find a DSers page URL when no MCP tool supports the requested feature. Ask before opening it. |

## Stores and platform settings

| Tool | Risk | Purpose |
|---|---|---|
| `list_stores` | read | List connected stores, supplier apps, currencies, Pricing Rule context, Shipping Profiles, and publishing capabilities. |
| `list_shopify_shipping_zones` | read | List Shopify Markets countries or regions for one owned Shopify store. |
| `create_shopify_shipping_profile` | write | Create one Shopify Shipping Profile with a zone and flat rate after explicit confirmation. |
| `list_supported_countries` | read | List DSers-supported countries with stable IDs and ISO codes. |
| `list_platform_shipping_methods` | read | List supplier shipping services for one platform and destination. |
| `start_store_connection` | write | Return the signed authorization URL needed to connect a visible seller or supplier app. |
| `start_store_reauthorization` | write | Return a reauthorization URL for one owned store. |

## Supplier discovery

| Tool | Risk | Purpose |
|---|---|---|
| `list_supplier_search_filters` | read | Discover supplier apps, categories, and supported search filters. |
| `search_supplier_products` | read | Search supplier products by keyword, image, category, price, and platform filters. |
| `list_featured_product_collections` | read | List curated product-idea collections. |
| `get_supplier_product` | read | Read supplier product options, variants, inventory, media, and source URL. |
| `list_supplier_product_shipping_methods_and_cost` | read | Read available shipping methods and freight quotes for a supplier product. |

## Pre-publish products and Pricing Rules

| Tool | Risk | Purpose |
|---|---|---|
| `list_pre_publish_products` | read | Search the DSers pre-publish/import list with stable pagination. |
| `get_pre_publish_product` | read | Read the editable snapshot and resource version of one pre-publish product. |
| `get_pre_publish_product_organization` | read | Read Category and Organization choices and current selections for up to 100 products. |
| `get_push_shipping_recommendation` | read | Read per-product, per-store shipping candidates while preparing a publish request. |
| `get_pricing_rule` | read | Read complete Basic, Advanced, and AI Custom Pricing Rule state for one store. |
| `create_pre_publish_products` | write | Add one to ten exact supplier products to the pre-publish list. |
| `update_pre_publish_product_content` | dangerous | Update content, media, package data, options, variants, SKUs, or stock using a current resource version. |
| `update_pre_publish_product_price` | dangerous | Update exact variant prices or one fixed price after preview and explicit confirmation. |
| `update_pre_publish_product_organization` | write | Update Category, Organization, or URL-handle fields for up to 100 products. |
| `update_pricing_rule` | write | Update supported Pricing Rule, cents, and store-exchange-rate sections. |
| `delete_pre_publish_products` | dangerous | Delete one to twenty exact pre-publish product IDs; filtered deletion first freezes exact IDs. |

## Managed products and Mapping

| Tool | Risk | Purpose |
|---|---|---|
| `list_managed_store_products` | read | Search DSers-managed store products and Mapping status. |
| `search_store_products` | read | Search seller-platform products and see whether each is already imported. |
| `get_managed_store_product` | read | Read seller variants, supplier mappings, media, sync settings, and inventory. |
| `get_store_product_mapping` | read | Read the MCP-safe Basic or Advanced Mapping view for a managed product. |
| `simulate_pricing_rule` | read | Calculate seller prices from supplied or mapped supplier costs. |
| `search_ai_mapping_candidates` | read | Search visual supplier candidates and read the latest AI Mapping Job status. |
| `start_ai_product_mapping` | dangerous | Create an AI Mapping computation for an exact product and supplier candidate. |
| `apply_ai_mapping` | dangerous | Apply one successful AI Mapping task after a fresh preview and second confirmation. |
| `apply_product_mapping` | dangerous | Update exact Basic or Advanced Mapping relationships and verify the saved result. |
| `import_store_products_to_dsers` | write | Import one to five exact seller products into DSers management. |
| `update_managed_store_product` | dangerous | Update selected seller variants with explicit automatic-sync decisions. |
| `update_store_product_price_in_bulk` | dangerous | Start a whole-store manual price-update task for one to twenty stores. |

## Publishing

| Tool | Risk | Purpose |
|---|---|---|
| `publish_products_to_stores` | dangerous | Publish selected pre-publish products to explicitly selected stores after a complete preflight. |
| `get_job_status` | read | Read normalized progress and results for an owner-scoped asynchronous task. |

Successful publishing returns `task_type` and `task_id`. Use both values with `get_job_status`; do not guess that task creation means every target has completed.

## Orders and fulfillment

| Tool | Risk | Purpose |
|---|---|---|
| `list_orders` | read | Search account-owned order summaries with cursor pagination and optional counts. |
| `get_order` | read | Read rich detail for one exact order, supplier platform, and order tab. |
| `diagnose_order_issue` | read | Explain placement blockers for exact orders or an order-search page. |
| `validate_order_address` | read | Validate the saved address against supported platform field rules. |
| `update_order_information` | dangerous | Update selected address, note, supplier message, or shipping-method fields. |
| `update_order_supplier` | dangerous | Change the supplier product or fulfillment variant for one order item. |
| `place_orders_to_suppliers` | dangerous | Start supplier placement for exact orders or normalized order filters. |
| `update_supplier_order` | write | Start an asynchronous supplier-order detail refresh. |
| `sync_existing_tracking_numbers_to_store` | dangerous | Send existing supplier tracking data to the sales channel after confirmation. |
| `update_tracking_numbers_to_store` | dangerous | Replace selected sales-side tracking numbers and send the new list to the sales channel. |
| `create_order_payment_link` | dangerous | Create the appropriate awaiting-payment checkout flow; it does not charge the user. |
| `send_buyer_tracking_notification` | dangerous | Trigger an asynchronous Shopify buyer tracking notification. |
| `cancel_supplier_order` | dangerous | Start cancellation of an eligible Agent or 1688 Dropshipping supplier order. |

## Packages and supplier changes

| Tool | Risk | Purpose |
|---|---|---|
| `list_packages` | read | List account-owned tracking packages, including exception views. |
| `get_package_details` | read | Read tracking details after ownership verification. |
| `sync_supply_order_tracking_number` | write | Start an asynchronous refresh for one account-owned tracking number. |
| `list_supplier_product_change_notifications` | read | List mapped supplier-product cost, stock, SKU, and availability changes. |

## Safety and consistency rules

- Tool inputs never expose `confirm`, `confirmation_id`, or `idempotency_key`.
- Dangerous operations require the agent to show the exact target and material effect, then obtain explicit user confirmation before the call.
- Callers must copy exact IDs from earlier DSers tool results and must not infer store, product, supplier, order, task, or variant identifiers.
- Versioned mutations must use the latest resource version returned by the relevant read tool.
- Writes with an uncertain upstream outcome are not automatically retryable. Re-read the current state before deciding the next step.
- Asynchronous creation is not final completion. Poll `get_job_status` with the returned `task_type + task_id`.

## Structured errors

Tool failures return machine-readable JSON. Important codes include:

- `UNAUTHORIZED` — the token is missing, invalid, or expired.
- `FORBIDDEN` — the identity is not allowed to use the tool or resource.
- `INSUFFICIENT_SCOPE` — reauthorize with the scopes listed in `required_scopes`.
- `PLAN_RESTRICTION` — the current plan or AI Credit entitlement does not allow the operation.
- `VALIDATION_ERROR` — correct the supplied arguments before retrying.
- `RATE_LIMITED` — wait for the returned rate-limit window.
- `UPSTREAM_TIMEOUT` or `UPSTREAM_ERROR` — retry only when `retryable` is true.
- `UPSTREAM_UNKNOWN` — a write may have reached DSers; inspect current state and do not automatically retry.
