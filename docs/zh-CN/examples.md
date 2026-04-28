# 示例

## 最小 Remote MCP 配置

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

在 `claude_desktop_config.json` 添加：

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

重启 Claude Desktop。首次调用工具时，使用 DSers 账号登录并授权。

## Claude Code

```bash
claude mcp add dsers https://mcp.dsers.com/dropshipping/mcp --transport http
claude mcp login dsers
```

## Cursor

打开：

```text
Settings → Tools & Integrations → MCP Tools → Add / Connect
```

填入：

```text
https://mcp.dsers.com/dropshipping/mcp
```

## Codex CLI

添加 MCP server 后执行：

```bash
codex mcp login dsers
```

如果客户端要求填写 URL，使用：

```text
https://mcp.dsers.com/dropshipping/mcp
```

## OpenClaw

```bash
openclaw mcp set dsers '{"url":"https://mcp.dsers.com/dropshipping/mcp","transport":"streamable-http"}'
```

## 推荐调用流程

### 单商品导入并推送草稿

1. `dsers_store_discover`
2. `dsers_product_import`
3. `dsers_product_preview`
4. 用户确认标题、价格、库存、变体
5. `dsers_store_push`，设置 `visibility_mode=backend_only`

### 带定价规则导入

```json
{
  "pricing": {
    "mode": "multiplier",
    "multiplier": 2
  }
}
```

推荐流程：

1. `dsers_store_discover`
2. `dsers_rules_validate`
3. `dsers_product_import`，传 `rules_json`
4. `dsers_product_preview`
5. `dsers_store_push`

### 批量导入

使用 `dsers_product_import` 的 `source_urls_json`：

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

批量响应默认是 summary；需要完整预览时设置 `batch_detail=full`。

### 已上架商品替换供应商

1. `dsers_store_discover` 获取 `store_id`
2. `dsers_my_products` 获取 `dsers_product_id`
3. `dsers_sku_remap`，先用 `mode=preview`
4. 检查每个 variant 的匹配结果
5. 确认无误后再用相同参数调用 `mode=apply`

## Rules JSON 速查

### 定价倍率

```json
{
  "pricing": {
    "mode": "multiplier",
    "multiplier": 2
  }
}
```

### 固定加价

```json
{
  "pricing": {
    "mode": "fixed_markup",
    "fixed_markup": 5
  }
}
```

### 固定价格

```json
{
  "pricing": {
    "mode": "fixed_price",
    "fixed_price": 19.99
  }
}
```

### 内容

```json
{
  "content": {
    "title_override": "Portable Mini Blender",
    "description_override_html": "<p>Compact USB rechargeable blender for travel and office use.</p>",
    "tags_add": ["kitchen", "portable"]
  }
}
```

### 图片

```json
{
  "images": {
    "keep_first_n": 5,
    "drop_indexes": [0, 3]
  }
}
```

### 变体覆盖

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

### Option edits

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
