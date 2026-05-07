# DSers MCP 使用指南

## 1. 简介

DSers MCP 是一个托管版 Model Context Protocol 服务，地址：

```text
https://mcp.dsers.com/dropshipping/mcp
```

它把 DSers 的选品、导入、定价、改标题/描述、推送到 Shopify/Wix、供应商替换等能力暴露给支持 MCP + OAuth 的客户端。

适用场景：

- 从 AliExpress / Alibaba / 1688 / Accio 导入商品到 DSers import list
- 给商品批量套定价、标题、描述、图片、变体规则
- 预览 DSers draft，再推送到 Shopify/Wix
- 搜索 DSers 商品池
- 浏览 import list 和已推送商品
- 对已上架商品做 SKU 级供应商替换

## 2. 前置条件

使用者需要：

- 一个可用的 DSers 账号
- DSers 已连接至少一个 Shopify / Wix 店铺
- 一个支持远程 HTTP MCP 和 OAuth 2.1 + PKCE 的 MCP 客户端

已验证或预期可用的客户端：

| 客户端 | 状态 | 说明 |
| --- | --- | --- |
| Claude Desktop | 可用 | 配置远程 HTTP MCP server |
| Cursor | 可用 | Settings → Tools & Integrations → MCP Tools |
| Claude Code | 可用 | `claude mcp add` |
| Codex CLI | 可用 | `codex mcp login` |
| VS Code / Cline / Windsurf / Zed / Continue | 预期可用 | 需要支持 OAuth MCP |
| OpenClaw | 可用 | 可通过 OpenClaw MCP registry 或当前可用的社区 MCP 目录接入 |

客户端选择原则：

- 这个 MCP 是远程 HTTP 服务，不提供本地 stdio server。
- 客户端必须支持 remote MCP 的 OAuth 2.1 + PKCE 授权流程。
- 如果某个客户端的 remote OAuth MCP 支持不完整，优先换 Claude Desktop、Claude Code、Cursor、Codex CLI，或通过 OpenClaw 社区 MCP 目录/网关接入。
- OpenClaw 的自由度较高，适合使用社区 MCP 资源或自定义 MCP 配置；实际可用 transport 以当前 OpenClaw 运行环境为准。

## 3. 初始化安装

### 3.1 Claude Desktop

在 `claude_desktop_config.json` 里添加：

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

重启 Claude Desktop。首次调用工具时，客户端会触发 OAuth，浏览器会打开 DSers 登录页。登录并授权后即可使用。

### 3.2 Claude Code

```bash
claude mcp add dsers https://mcp.dsers.com/dropshipping/mcp --transport http
```

然后触发登录：

```bash
claude mcp login dsers
```

### 3.3 Cursor

路径：

```text
Settings → Tools & Integrations → MCP Tools → Add / Connect
```

填入：

```text
https://mcp.dsers.com/dropshipping/mcp
```

Cursor 会自动走 OAuth 授权流程。

### 3.4 Codex CLI

配置 MCP server 后执行：

```bash
codex mcp login dsers
```

如果客户端要求 URL，使用：

```text
https://mcp.dsers.com/dropshipping/mcp
```

### 3.5 OpenClaw

OpenClaw 可以通过 MCP registry 保存远程 MCP server 配置。自托管或 CLI 场景可参考：

```bash
openclaw mcp set dsers '{"url":"https://mcp.dsers.com/dropshipping/mcp","transport":"streamable-http"}'
```

也可以通过当前可用的社区 MCP 目录添加 DSers MCP。这些目录不是 DSers 运营的服务，请以对应目录的当前连接说明为准，并优先使用上方 DSers 官方 MCP endpoint。

## 4. 认证模型

DSers MCP 使用 OAuth 2.1 + PKCE。不要手动配置 API key，也不要手动写 `Authorization` header。

流程：

1. 在 MCP 客户端中添加 DSers MCP server URL
2. 首次调用工具时，客户端打开授权页面
3. 使用 DSers 账号登录并完成授权
4. 回到 MCP 客户端后即可调用工具

注意：

- 授权失败时，优先在 MCP 客户端里重新登录或重新连接 DSers MCP
- 不要把 DSers 后台 API key、店铺 token 或浏览器 cookie 粘贴给 MCP 客户端
- 团队环境建议使用独立 DSers 账号授权，避免多人共享个人账号

## 5. 推荐调用流程

### 5.1 单商品导入并推送草稿

1. `dsers_store_discover`
2. `dsers_product_import`
3. `dsers_product_preview`
4. 用户确认标题、价格、库存、变体
5. `dsers_store_push`，设置 `visibility_mode=backend_only`

### 5.2 带定价规则导入

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

### 5.3 批量导入

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

### 5.4 已上架商品替换供应商

1. `dsers_store_discover` 获取 `store_id`
2. `dsers_my_products` 获取 `dsers_product_id`
3. `dsers_sku_remap`，先用 `mode=preview`
4. 检查每个 variant 的匹配结果
5. 确认无误后再用相同参数调用 `mode=apply`

## 6. 工具清单

### dsers_store_discover

获取当前 DSers 账号下可用店铺和规则能力。所有流程建议先调用它。

常用参数：

- `target_store`：可选。按 store ID 或名称过滤。

返回重点：

- `stores[].id`
- `stores[].name`
- `stores[].platform`
- `stores[].ship`
- `rules.pricing`
- `rules.content`
- `rules.images`
- `plan_issue`

### dsers_rules_validate

校验规则对象是否能被当前 DSers/店铺能力支持。适合在导入前检查 pricing/content/images/variant rules。

常用参数：

- `rules`：JSON 字符串
- `target_store`：可选

示例：

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

搜索 DSers 商品池。支持关键词搜索和图片搜索。

常用参数：

- `keyword`：关键词
- `image_url`：图片 URL，传了它会优先走视觉搜索
- `supplier`：`aliexpress`、`alibaba`、`ali1688`
- `ship_to`：目的国家，默认 `US`
- `ship_from`：发货国家
- `sort`：`relevance`、`newest`、`price`
- `limit`：每页数量，最大 50
- `search_after`：翻页 cursor

返回结果里的 `import_url` 可以直接传给 `dsers_product_import`。

### dsers_product_import

把供应商商品导入 DSers import list，并返回预览。支持单个 URL 或批量 URL。

常用参数：

- `source_url`：单商品 URL
- `source_urls_json`：批量 JSON 数组
- `source_hint`：`auto`、`aliexpress`、`alibaba`、`accio`
- `country`：国家代码，默认 `US`
- `target_store`：店铺 ID 或名称
- `visibility_mode`：`backend_only` 或 `sell_immediately`
- `rules_json`：规则 JSON 字符串
- `batch_detail`：`summary` 或 `full`

也可以用扁平参数快速设置规则：

- `pricing_mode`
- `pricing_multiplier`
- `pricing_fixed_markup`
- `pricing_fixed_price`
- `title_override`
- `title_prefix`
- `title_suffix`
- `description_override_html`
- `description_append_html`

返回重点：

- `import_item_id`
- `title`
- `price_summary`
- `variant_count`
- `active_rules`

`import_item_id` 是后续 preview/update/push/delete 的核心 handle。

### dsers_product_preview

重新读取已导入商品的 draft preview。

常用参数：

- `import_item_id`
- `variant_detail`：`compact` 或 `full`
- `variant_offset`
- `variant_limit`
- `show_all_options`
- `include_images`

使用建议：

- 默认 `compact` 适合快速看所有 SKU
- 需要成本、compare_at、supplier_qty 时用 `variant_detail=full`
- 修改 option 前用 `show_all_options=true`
- 做供应商匹配时用 `include_images=true`

### dsers_product_update_rules

更新已导入商品的规则。适合先导入，再由 LLM 改标题、描述、价格或变体。

常用参数：

- `import_item_id`
- `rules_json`
- `target_store`
- `visibility_mode`

规则合并方式：

- `pricing`、`images`、`variant_overrides` 按 family 替换
- `title_prefix`、`title_suffix`、`description_append_html` 是 slot，重复调用会替换旧 slot
- `option_edits` 是完整替换，不是增量合并
- 传 `{"pricing": null}` 可移除该规则 family

如果规则持久化到 DSers 失败，工具会报错，避免后续 push 使用旧 draft。

### dsers_import_list

浏览 DSers import list，也就是待推送商品列表。

常用参数：

- `page`
- `page_size`

返回重点：

- `import_item_id`
- `title`
- `sell_price_range`
- `cost_range`
- `variant_count`
- `total_stock`
- `push_status`
- `source_url`

### dsers_my_products

浏览某个店铺里已经推送过的 DSers 商品。

常用参数：

- `store_id`：来自 `dsers_store_discover`
- `page`
- `page_size`

返回重点：

- `dsers_product_id`
- `title`
- `sell_price`
- `cost`
- `status`
- `supplier_url`

`dsers_product_id` 是 `dsers_sku_remap` 的输入。

### dsers_store_push

把 import draft 推送到 Shopify/Wix。

常用参数：

- `import_item_id`
- `import_item_ids_json`
- `target_store`
- `target_stores_json`
- `visibility_mode`
- `push_options_json`
- `force_push`

推荐默认：

```json
{
  "visibility_mode": "backend_only"
}
```

安全机制：

- 售价低于成本会 block
- 售价为 0 会 block
- 所有 variants 零库存会 block
- 空 variants 会 block
- 低利润、低库存、低价会 warning

只有在明确告知风险并获得用户确认后，才允许使用 `force_push=true`。

### dsers_product_delete

从 DSers import list 删除商品。不可恢复。

常用参数：

- `import_item_id`
- `confirm`

调用协议：

1. 第一次调用不要传 `confirm=true`
2. 把返回的确认信息展示给用户
3. 用户明确确认后，再传 `confirm=true`

注意：它只删除 DSers import list 中的 draft，不删除已经发布到店铺前台的商品。

### dsers_sku_remap

替换已上架商品的供应商，并做 SKU/variant 级匹配。

两种模式：

- Strict：传 `new_supplier_url`，使用指定供应商
- Discover：不传 `new_supplier_url`，工具基于图片反搜候选供应商

常用参数：

- `dsers_product_id`：来自 `dsers_my_products`
- `store_id`：来自 `dsers_store_discover`
- `new_supplier_url`：可选
- `mode`：`preview` 或 `apply`
- `country`：默认 `US`
- `auto_confidence`：默认 70
- `max_candidates`：Discover 模式候选数量

正确流程：

1. 先 `mode=preview`
2. 检查 `diffs`、`summary`、`top_candidates`、`warnings`
3. 确认匹配正确
4. 再 `mode=apply`

不要跳过 preview 直接 apply。

## 7. Prompt 模板

服务端还提供 4 个 prompt/workflow 模板：

| 名称 | 用途 |
| --- | --- |
| `dsers_workflow_quick-import` | 单商品导入并推送草稿 |
| `dsers_workflow_bulk-import` | 多商品批量导入并套价格倍率 |
| `dsers_workflow_multi-push` | 一个商品推送到所有已连接 Shopify/Wix 店铺 |
| `dsers_workflow_seo-optimize` | 导入后由 LLM 改标题/描述，再推送 |

这些是客户端可选的快捷工作流，不是必须调用的工具。

## 8. 规则 JSON 速查

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

### Variant overrides

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

## 9. 常见错误处理

| 现象 | 处理 |
| --- | --- |
| 客户端提示 unauthorized | 重新触发 MCP OAuth 登录，不要手动填 API key |
| 授权后仍无法调用 | 移除 MCP server 后重新添加，重新授权 |
| push 被 blocked | 先看 blocked 原因，修价格/库存/variants，不要直接 force |
| import_item_id 找不到 | 调 `dsers_import_list` 重新确认 |
| store_id 不确定 | 调 `dsers_store_discover` |
| supplier 替换匹配不确定 | 只做 `dsers_sku_remap mode=preview`，不要 apply |

## 10. 开发者注意事项

- 所有金额单位对外按美元展示
- `store_id`、`dsers_product_id` 用字符串，不要用 JS number
- `import_item_id` 是 import list draft 的 handle
- `dsers_product_id` 是已推送商品的 handle
- 默认 push 应使用 `backend_only`
- `sell_immediately` 必须先确认价格、库存、店铺和 variants
- 删除 import item 必须二次确认
- `force_push` 必须明确告知用户风险后才可使用
