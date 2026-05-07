# Examples

## Minimal Remote MCP Configuration

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

## Claude Desktop

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

Restart Claude Desktop. On first tool use, sign in with your DSers account and authorize access.

## Claude Code

```bash
claude mcp add dsers https://mcp.dsers.com/dropshipping/mcp --transport http
claude mcp login dsers
```

## Cursor

Open:

```text
Settings → Tools & Integrations → MCP Tools → Add / Connect
```

Use:

```text
https://mcp.dsers.com/dropshipping/mcp
```

## Codex CLI

After adding the MCP server, run:

```bash
codex mcp login dsers
```

If the client asks for the server URL, use:

```text
https://mcp.dsers.com/dropshipping/mcp
```

## OpenClaw

OpenClaw support varies by deployment. If your OpenClaw runtime supports remote HTTP MCP and OAuth, use its MCP registry or community gateway flow with:

```text
https://mcp.dsers.com/dropshipping/mcp
```

## Recommended Workflows

### Import One Product and Push as Draft

1. `dsers_store_discover`
2. `dsers_product_import`
3. `dsers_product_preview`
4. Confirm title, price, inventory, and variants
5. `dsers_store_push` with `visibility_mode=backend_only`

### Import with Pricing Rules

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

### Batch Import

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

### Replace a Supplier on an Existing Product

1. `dsers_store_discover` to get `store_id`
2. `dsers_my_products` to get `dsers_product_id`
3. `dsers_sku_remap` with `mode=preview`
4. Review the variant-level matching plan
5. Call `dsers_sku_remap` again with `mode=apply`

## Rules JSON Cheat Sheet

### Pricing Multiplier

```json
{
  "pricing": {
    "mode": "multiplier",
    "multiplier": 2
  }
}
```

### Fixed Markup

```json
{
  "pricing": {
    "mode": "fixed_markup",
    "fixed_markup": 5
  }
}
```

### Fixed Price

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
