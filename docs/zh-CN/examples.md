# 使用示例

这些示例是推荐的工具调用顺序，不是服务端提供的 MCP Prompt。必须从之前的结果复制精确 ID 和 version，禁止猜测。

## 连接客户端

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

首次连接时在客户端界面完成 OAuth。

## 搜索并查看供应商商品

用户请求：

> 从我可用的供应商应用中找无线标签打印机，给我两个候选，并估算发往美国的运费。不要导入。

推荐顺序：

1. `list_supplier_search_filters` 获取可用 supplier platform ID 和搜索条件。
2. `search_supplier_products` 使用返回的精确 platform ID 和用户条件搜索。
3. `get_supplier_product` 获取所选候选详情。
4. `list_supplier_product_shipping_methods_and_cost` 查询目的地物流。

## 加入私有待发布列表

用户请求：

> 把选中的供应商商品以英文加入待发布列表，然后展示当前内容和价格。不要发布。

推荐顺序：

1. 调用 `create_pre_publish_products`，在 `items` 数组中提供精确 supplier platform 和 product ID。
2. 使用返回的 import item ID 调用 `get_pre_publish_product`。
3. 展示商品，并为后续修改保留当前 resource version。

## 修改待发布商品

不同类型的修改必须走不同流程：

- 内容、媒体、选项、变体、SKU、库存和包裹信息使用 `update_pre_publish_product_content`。
- 精确变体价格或统一价格使用 `update_pre_publish_product_price`。
- Category、Organization 和 URL handle 先调用 `get_pre_publish_product_organization`，再调用 `update_pre_publish_product_organization`。
- 店铺 Pricing Rule 使用 `get_pricing_rule`，可选调用 `simulate_pricing_rule`，最后调用 `update_pricing_rule`。

dangerous 的价格或内容修改前，必须展示精确的前后变化并取得用户明确确认。写入后重新读取。

## 发布商品

用户请求：

> 准备把这两个待发布商品发布到 Store A。先展示物流选择和精确目标。

推荐顺序：

1. 调用 `list_stores`，把 Store A 解析成精确 `dsers_store_id`。
2. 对每件商品调用 `get_pre_publish_product`。
3. 对精确商品-店铺目标调用 `get_push_shipping_recommendation`。
4. 展示预检、目标店铺与所选物流。
5. 用户明确确认后，只调用一次 `publish_products_to_stores`。
6. 使用返回的 `task_type + task_id` 查询 `get_job_status`。

任务创建成功不代表发布已经完成。

## 查看或修改供应商 Mapping

只读流程：

1. `list_managed_store_products` 或 `search_store_products`。
2. `get_managed_store_product`。
3. `get_store_product_mapping`。

手工修改时，准备精确 Mapping 关系，在用户确认最终目标后调用 `apply_product_mapping`。AI Mapping 使用 `search_ai_mapping_candidates`、`start_ai_product_mapping`，轮询 `get_job_status`，最后单独确认并调用 `apply_ai_mapping`。

## 诊断并处理订单

用户请求：

> 显示指定供应商的 Awaiting Order，并解释阻止下单的原因。暂时不要下单。

推荐顺序：

1. 使用精确 supplier platform 和 status 调用 `list_orders`。
2. 使用同一组 supplier/status 证据调用 `diagnose_order_issue`。
3. 只有需要完整详情或修复时才调用 `get_order`。
4. 使用对应订单修改工具执行用户明确要求的修复。
5. 重新读取并再次诊断。
6. 用户明确确认后调用 `place_orders_to_suppliers`。
7. 如果结果返回 `task_type + task_id`，继续查询任务状态。

## 删除待发布商品

按精确 ID 删除时，先展示目标并取得确认，再调用 `delete_pre_publish_products`。按条件删除时，先用 `list_pre_publish_products` 固定精确 ID。工具不接受 `confirm` 参数。

## 请求体示例

`create_pre_publish_products` 单次最多接受 10 个 item：

```json
{
  "items": [
    { "supplier_platform_id": "1", "supplier_product_id": "1005000000000000" },
    { "supplier_platform_id": "2", "supplier_product_id": "60123456789" }
  ]
}
```

`update_pre_publish_product_content` 按 `import_item_id` 编辑内容、媒体和变体：

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

`update_pre_publish_product_price` 可以设置统一固定价，或通过 `supplier_product_variant_list` 编辑指定变体行：

```json
{
  "import_item_id": "123456",
  "dsers_store_id": "789",
  "mode": "fixed_price",
  "fixed_price": "19.99"
}
```

## 处理失败

- `INSUFFICIENT_SCOPE`：按 `required_scopes` 重新授权。
- `PLAN_RESTRICTION`：解释返回的套餐或 AI Credits 限制。
- `RATE_LIMITED`：等待返回的重试时间窗。
- `VALIDATION_ERROR`：修正参数后再调用。
- `UPSTREAM_UNKNOWN`：禁止重试写操作，先重新读取资源。
