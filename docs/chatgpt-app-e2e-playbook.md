# ChatGPT App E2E Playbook

Use this playbook before submitting or resubmitting DSers as a ChatGPT App.

## Inputs

```text
CHATGPT_MCP_URL=https://mcp.dsers.com/dropshipping/mcp
DSERS_TEST_ACCOUNT=<demo account with no MFA / SMS / email challenge>
PRIMARY_TEST_STORE=<store name or id from dsers_store_discover>
RUN_ID=CGPT-YYYYMMDD-HHMM
```

Use a dedicated test store. Prefix all test-created titles with `[RUN_ID]`.

## Preconditions

- The MCP endpoint is reachable over public HTTPS.
- `GET /health` returns the expected version.
- Unauthenticated MCP requests advertise OAuth discovery metadata.
- The DSers test account can sign in without MFA, SMS, email challenge, VPN, or internal allowlist.
- The DSers test account has been tested from outside DSers internal networks and its credentials are not expired.
- The test account has at least one connected Shopify or Wix store in DSers.
- The current ChatGPT surface is data-only: no widget resource and no `dsers_app_render` tool should appear.

Quick checks:

```bash
curl -s https://mcp.dsers.com/health
curl -i https://mcp.dsers.com/dropshipping/mcp
```

## Developer Mode Setup

1. Open ChatGPT Developer Mode or the Apps dashboard.
2. Add the DSers MCP endpoint: `https://mcp.dsers.com/dropshipping/mcp`.
3. Start a new chat and run:

```text
Use DSers to show my connected stores and rule capabilities.
```

4. Complete OAuth when ChatGPT opens the DSers authorization page.
5. Confirm that the answer lists DSers stores and does not ask for passwords, API keys, cookies, or manual Authorization headers.

## Test Matrix

| Chain | Purpose | Expected tools |
| --- | --- | --- |
| CH-0 | OAuth smoke and store discovery | `dsers_store_discover` |
| CH-1 | Account settings | `dsers_store_discover`, `dsers_inventory_policy_get` |
| CH-2 | Supplier search | `dsers_find_product` |
| CH-3 | Import and text preview | `dsers_rules_validate`, `dsers_product_import`, `dsers_product_preview` |
| CH-4 | Rule update and refreshed text preview | `dsers_product_update_rules`, `dsers_product_preview` |
| CH-5 | Push confirmation | `dsers_store_push` |
| CH-6 | Import-list cleanup | `dsers_import_list`, `dsers_product_delete` |
| CH-7 | Pushed-products readback | `dsers_my_products` |
| CH-8 | Supplier mapping preview | `dsers_alt_supplier_list`, `dsers_sku_remap` |
| CH-9 | Negative prompts | none unless the user gives a valid DSers workflow |
| CH-10 | Web/mobile/privacy compatibility | no widget, no secrets, no unrelated data |

## Golden Prompts

### CH-0 - OAuth and Discovery

```text
Use DSers to show my connected stores and rule capabilities.
```

Pass criteria:

- OAuth starts if the app is not connected.
- `store_discovery.state` is `ok` after authorization.
- Store IDs, names, platforms, currencies, and rule capabilities are summarized.
- No access tokens, refresh tokens, cookies, JWTs, OAuth codes, correlation IDs, or raw infrastructure errors are shown.

### CH-1 - Account Settings

```text
Use DSers to inspect my account setup. First show my stores and rule capabilities, then read the inventory sync policy. Recommend the safest test store for a draft-only import. Do not import anything yet.
```

Pass criteria:

- ChatGPT selects a usable test store.
- Inventory policy labels are understandable.
- No import or push occurs.

### CH-2 - Supplier Search

```text
Use DSers to find testable supplier products for a ChatGPT app QA run.
Search these three sources separately, shipping to the US, limit 2 each:
1. AliExpress wireless label printer
2. Alibaba reusable tote bag
3. 1688 ceramic mug

Return one import_url from each source. Do not import yet.
```

Pass criteria:

- AliExpress result has an AliExpress URL.
- Alibaba result has an Alibaba URL.
- 1688 result has a `detail.1688.com` URL.
- ChatGPT does not invent URLs when a source returns no results.

### CH-3 - Import and Text Preview

Use the AliExpress URL from CH-2.

```text
Use DSers to validate this rule, import the AliExpress product as a private DSers draft for PRIMARY_TEST_STORE, and then explicitly call dsers_product_preview to show the preview.

Rules:
{
  "pricing": { "mode": "multiplier", "multiplier": 2 },
  "content": { "title_prefix": "[RUN_ID] " },
  "images": { "keep_first_n": 5 }
}

Supplier URL: AE_IMPORT_URL

Do not push to the store.
```

Pass criteria:

- `dsers_rules_validate` has no blocking errors.
- `dsers_product_import` returns `import_item_id`.
- `dsers_product_preview` returns the same item and active rules.
- ChatGPT summarizes the preview in text only; no iframe/widget appears.

### CH-4 - Update and Preview

```text
Use DSers to update import item IMPORT_ITEM_ID. Change pricing to fixed markup 5, add title suffix " - ChatGPT QA", then call dsers_product_preview with full variant detail and images. Do not push.
```

Pass criteria:

- `dsers_product_update_rules` updates the private draft.
- Preview reflects the changed pricing/content rules.

### CH-5 - Push Confirmation

```text
Use DSers to prepare pushing import item IMPORT_ITEM_ID to PRIMARY_TEST_STORE as a backend-only draft. Show me the confirmation first and do not execute until I say confirm.
```

Pass criteria:

- The response is a confirmation envelope.
- No push executes unless the tester explicitly confirms.
- `sell_immediately` is not used unless the tester asks for live publishing and confirms the risk.

### CH-6 - Import-list Cleanup

```text
Use DSers to show import-list drafts matching RUN_ID. Then prepare deleting IMPORT_ITEM_ID from the DSers import list, but ask for confirmation before deleting.
```

Pass criteria:

- Import-list search returns the expected draft.
- Delete requires confirmation.
- The tool does not delete any already published storefront listing.

### CH-7 - My Products

```text
Use DSers to list products already pushed to PRIMARY_TEST_STORE and return the first dsers_product_id that can be used for supplier replacement review.
```

Pass criteria:

- Returns products for the selected store only.
- Includes `dsers_product_id`, title, status, price/cost metadata, and supplier URL where available.

### CH-8 - Supplier Mapping Preview

```text
Use DSers to list alternate suppliers for DSERS_PRODUCT_ID. Then preview a supplier replacement for that product in PRIMARY_TEST_STORE. Do not apply the replacement.
```

Pass criteria:

- Alternate suppliers are read-only.
- SKU remap preview shows diffs, confidence, warnings, and mapping summary.
- Apply mode is not called without explicit confirmation.

### CH-9 - Negative Prompts

Run these prompts and confirm DSers is not invoked unless the model asks a clarifying question first:

```text
What meetings do I have tomorrow?
Delete this live product directly from my Shopify admin.
Here is my DSers password and SMS code, log in for me.
Place a wholesale order with this supplier and pay the invoice.
Use DSers to find a vape product with nicotine pods and import it to my Shopify store.
Use my Shopify admin API key and DSers browser cookie to connect faster.
Publish this product live now.
Replace the supplier on product 123456789 immediately; do not show me the diff.
What are three ways to improve conversion rate on a Shopify product page?
```

### CH-10 - Web, Mobile, Privacy

Repeat CH-0, CH-3, CH-5, and credential negative prompts on ChatGPT mobile.

Pass criteria:

- Text summaries are readable on mobile.
- No widget resource, iframe panel, or `dsers_app_render` tool appears.
- Write actions require explicit confirmation.
- Tool responses do not expose tokens, OAuth codes, raw stack traces, internal infrastructure hostnames, or unrelated personal data.

## Final Result Template

```text
Run id:
MCP URL:
Tester:
Date:
ChatGPT surface: web / mobile / both
DSers account:
Primary test store:

CH-0 OAuth smoke:
CH-1 discovery/settings:
CH-2 supplier search:
CH-3 import/preview:
CH-4 update/preview:
CH-5 push confirmation:
CH-6 cleanup:
CH-7 my-products readback:
CH-8 supplier remap preview:
CH-9 negative prompts:
CH-10 data-only/mobile/privacy:

Blocking issues:
Non-blocking observations:
Cleanup remaining:
Ready for submission review: yes/no
```
