# 工具清单

DSers Official MCP Server 提供面向 dropshipping 工作流的 AI 工具。

## 推荐优先调用

### `dsers_store_discover`

获取当前 DSers 账号下可用店铺和规则能力。多数流程建议先调用它。

常用参数：

- `target_store`：可选。按 store ID 或名称过滤。

返回重点：

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

`store_discovery.state` 用于区分已连接店铺、无店铺、授权问题、上游异常、响应结构变化和目标店铺未命中。成功发现店铺时，也可能返回 `mcp_update`，提示客户端刷新工具、重新授权或重装 MCP 连接。

## 规则校验

### `dsers_rules_validate`

校验规则对象是否能被当前 DSers/店铺能力支持。适合在导入或更新前检查 pricing/content/images/variant rules。

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

## 商品搜索与导入

### `dsers_find_product`

搜索 DSers 商品池，支持关键词搜索和图片搜索。

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

### `dsers_product_import`

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

## 草稿预览与更新

### `dsers_product_preview`

重新读取已导入商品的 draft preview。

常用参数：

- `import_item_id`
- `variant_detail`：`compact` 或 `full`
- `variant_offset`
- `variant_limit`
- `show_all_options`
- `include_images`

使用建议：

- 默认 `compact` 适合快速看所有 SKU。
- 需要成本、compare_at、supplier_qty 时用 `variant_detail=full`。
- 修改 option 前用 `show_all_options=true`。
- 做供应商匹配时用 `include_images=true`。

### `dsers_product_update_rules`

更新已导入商品的规则，适合先导入，再由 LLM 改标题、描述、价格或变体。

常用参数：

- `import_item_id`
- `rules_json`
- `target_store`
- `visibility_mode`

规则合并方式：

- `pricing`、`images`、`variant_overrides` 按 family 替换。
- `title_prefix`、`title_suffix`、`description_append_html` 是 slot，重复调用会替换旧 slot。
- `option_edits` 是完整替换，不是增量合并。
- `remove_value` option edit 会删除匹配的 draft variants。
- 传 `{"pricing": null}` 可移除该规则 family。

如果规则持久化到 DSers 失败，工具会报错，避免后续 push 使用旧 draft。

## 列表与已发布商品

### `dsers_import_list`

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

### `dsers_my_products`

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

## 店铺推送

### `dsers_store_push`

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

## 删除

### `dsers_product_delete`

从 DSers import list 删除商品，不可恢复。

常用参数：

- `import_item_id`
- `confirm`

调用协议：

1. 第一次调用不要传 `confirm=true`。
2. 把返回的确认信息展示给用户。
3. 用户明确确认后，再传 `confirm=true`。

注意：它只删除 DSers import list 中的 draft，不删除已经发布到店铺前台的商品。

## 库存策略

### `dsers_inventory_policy_get`

读取 DSers 账号库存同步策略。

返回重点：

- 供应商商品不可用时的处理方式
- 单个 variant 不可用时的处理方式
- 是否自动同步库存
- 每个设置的可读标签

这是只读工具，不会修改账号设置。

## 供应商替换

### `dsers_alt_supplier_list`

列出某个 DSers 商品已经绑定的主供应商和备选供应商。

常用参数：

- `dsers_product_id`
- `variant_detail`

返回重点：

- 供应商 URL
- 供应商平台和 app ID
- 商品标题和图片
- variant 数量
- 供应商原生币种和币种来源

选择要传给 `dsers_sku_remap` 的供应商 URL 前，建议先调用这个工具。

### `dsers_sku_remap`

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

1. 先 `mode=preview`。
2. 检查 `diffs`、`summary`、`top_candidates`、`warnings`。
3. 确认匹配正确。
4. 再 `mode=apply`。

不要跳过 preview 直接 apply。

## Prompt 模板

服务端还提供 4 个 prompt/workflow 模板：

| 名称 | 用途 |
| --- | --- |
| `dsers_workflow_quick-import` | 单商品导入并推送草稿 |
| `dsers_workflow_bulk-import` | 多商品批量导入并套价格倍率 |
| `dsers_workflow_multi-push` | 一个商品推送到所有已连接 Shopify/Wix 店铺 |
| `dsers_workflow_seo-optimize` | 导入后由 LLM 改标题/描述，再推送 |

这些是客户端可选的快捷工作流，不是必须调用的工具。
