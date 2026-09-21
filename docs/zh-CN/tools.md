# 工具清单

DSers Official MCP Server 当前提供 60 个工具。服务只声明 MCP Tools，不提供 MCP Prompts 或 Resources。

客户端看到的工具会根据登录身份的 role 和 OAuth scopes 过滤。所有已注册工具都支持 `admin`、`dsers`、`mcp` 三类 role；绝大多数工具还要求一个或多个 scope，`find_feature_entry` 是唯一明确不访问受保护业务资源的工具。

## 账号、套餐、账单与导航

| 工具 | 风险 | 用途 |
|---|---|---|
| `get_account_profile` | read | 获取当前账号资料、时区、权限和子账号。 |
| `get_plan_and_credits` | read | 获取有效套餐、权益与 AI Credits 余额。 |
| `get_plan_and_credit_billing` | read | 查询套餐购买或 AI Credits 用量，或获取发票下载地址。 |
| `get_order_paymentlink_and_billing` | read | 查询账号所属的订单账单记录。 |
| `recommend_apps_to_install` | read | 查找当前用户可见但尚未安装的销售或供应商应用，不会自动安装。 |
| `find_feature_entry` | read | 当没有合适 MCP 工具时查找 DSers 页面入口；打开前应先询问用户。 |

## 店铺与平台设置

| 工具 | 风险 | 用途 |
|---|---|---|
| `list_stores` | read | 获取已连接店铺、供应商应用、币种、Pricing Rule、Shipping Profile 与发布能力。 |
| `list_shopify_shipping_zones` | read | 获取指定 Shopify 店铺的 Markets 国家或地区。 |
| `create_shopify_shipping_profile` | write | 在用户明确确认后，为指定 Shopify 店铺创建一个带区域和平邮费率的 Shipping Profile。 |
| `list_supported_countries` | read | 获取 DSers 支持的国家、稳定 ID 与 ISO code。 |
| `list_platform_shipping_methods` | read | 获取指定供应商平台和目的地支持的物流服务。 |
| `start_store_connection` | write | 返回连接可见销售或供应商应用所需的签名授权地址。 |
| `start_store_reauthorization` | write | 返回指定自有店铺的重新授权地址。 |

## 供应商选品

| 工具 | 风险 | 用途 |
|---|---|---|
| `list_supplier_search_filters` | read | 获取供应商应用、分类以及支持的搜索条件。 |
| `search_supplier_products` | read | 按关键词、图片、分类、价格和平台条件搜索供应商商品。 |
| `list_featured_product_collections` | read | 获取精选商品灵感集合。 |
| `get_supplier_product` | read | 获取供应商商品的选项、变体、库存、媒体与来源地址。 |
| `list_supplier_product_shipping_methods_and_cost` | read | 获取供应商商品可用的物流方式与运费报价。 |

## 待发布商品与 Pricing Rule

| 工具 | 风险 | 用途 |
|---|---|---|
| `list_pre_publish_products` | read | 分页搜索 DSers 待发布商品列表。 |
| `get_pre_publish_product` | read | 获取一件待发布商品的可编辑快照和 resource version。 |
| `get_pre_publish_product_organization` | read | 获取最多 100 件商品的 Category、Organization 可选项与当前选择。 |
| `get_push_shipping_recommendation` | read | 准备发布时，按商品和店铺读取物流候选。 |
| `get_pricing_rule` | read | 获取指定店铺完整的 Basic、Advanced 与 AI Custom Pricing Rule。 |
| `create_pre_publish_products` | write | 将 1–10 个精确供应商商品加入待发布列表。 |
| `update_pre_publish_product_content` | dangerous | 使用当前 resource version 修改内容、媒体、包裹、选项、变体、SKU 或库存。 |
| `update_pre_publish_product_price` | dangerous | 在预览和明确确认后修改精确变体价格或设置统一价格。 |
| `update_pre_publish_product_organization` | write | 批量修改最多 100 件商品的 Category、Organization 或 URL handle。 |
| `update_pricing_rule` | write | 更新支持的 Pricing Rule、尾数与店铺汇率配置。 |
| `delete_pre_publish_products` | dangerous | 删除 1–20 个精确待发布商品 ID；按条件删除前必须先冻结精确 ID。 |

## 已发布商品与 Mapping

| 工具 | 风险 | 用途 |
|---|---|---|
| `list_managed_store_products` | read | 搜索 DSers 管理的店铺商品与 Mapping 状态。 |
| `search_store_products` | read | 搜索销售平台商品，并标明是否已导入 DSers。 |
| `get_managed_store_product` | read | 获取销售变体、供应商 Mapping、媒体、同步设置与库存。 |
| `get_store_product_mapping` | read | 获取已管理商品的 MCP-safe Basic 或 Advanced Mapping。 |
| `simulate_pricing_rule` | read | 使用提供或已映射的供应商成本计算销售价格。 |
| `search_ai_mapping_candidates` | read | 图片搜索供应商候选，并返回最近一次 AI Mapping Job 状态。 |
| `start_ai_product_mapping` | dangerous | 为精确商品和供应商候选创建 AI Mapping 计算任务。 |
| `apply_ai_mapping` | dangerous | 在重新预览和二次确认后应用一个成功的 AI Mapping task。 |
| `apply_product_mapping` | dangerous | 更新精确的 Basic 或 Advanced Mapping 关系并校验保存结果。 |
| `import_store_products_to_dsers` | write | 将同一店铺的 1–5 个精确销售商品导入 DSers 管理。 |
| `update_managed_store_product` | dangerous | 修改选定销售变体，并明确处理相关自动同步设置。 |
| `update_store_product_price_in_bulk` | dangerous | 为 1–20 个店铺创建全店手工价格更新任务。 |

## 发布

| 工具 | 风险 | 用途 |
|---|---|---|
| `publish_products_to_stores` | dangerous | 完整预检后，将选定待发布商品发布到明确选择的店铺。 |
| `get_job_status` | read | 查询当前账号所属异步任务的标准化进度与结果。 |

发布成功创建任务时会返回 `task_type` 和 `task_id`。必须把两者原样传给 `get_job_status`；创建任务不代表所有目标已经完成。

## 订单与履约

| 工具 | 风险 | 用途 |
|---|---|---|
| `list_orders` | read | 使用 cursor 分页查询账号所属订单摘要和可选计数。 |
| `get_order` | read | 获取指定订单、供应商平台与订单 tab 的完整详情。 |
| `diagnose_order_issue` | read | 诊断精确订单或一页搜索结果的下单阻塞原因。 |
| `validate_order_address` | read | 按平台支持的字段规则校验订单当前保存地址。 |
| `update_order_information` | dangerous | 修改选定地址、备注、供应商留言或物流方式字段。 |
| `update_order_supplier` | dangerous | 修改一个订单商品的供应商商品或履约变体。 |
| `place_orders_to_suppliers` | dangerous | 按精确订单或标准化筛选条件发起供应商下单。 |
| `update_supplier_order` | write | 发起异步供应商订单详情刷新。 |
| `sync_existing_tracking_numbers_to_store` | dangerous | 确认后把已有供应商 Tracking 数据发送到销售平台。 |
| `update_tracking_numbers_to_store` | dangerous | 替换选定销售商品的 Tracking Number 列表并发送到销售平台。 |
| `create_order_payment_link` | dangerous | 创建对应的待付款 checkout 流程，本工具不会直接扣款。 |
| `send_buyer_tracking_notification` | dangerous | 异步触发 Shopify 买家物流通知。 |
| `cancel_supplier_order` | dangerous | 发起符合条件的 Agent 或 1688 Dropshipping 供应商订单取消。 |

## 包裹与供应商品变更

| 工具 | 风险 | 用途 |
|---|---|---|
| `list_packages` | read | 获取账号所属包裹，包括异常视图。 |
| `get_package_details` | read | 通过归属校验后读取物流详情。 |
| `sync_supply_order_tracking_number` | write | 为一个账号所属 Tracking Number 发起异步刷新。 |
| `list_supplier_product_change_notifications` | read | 获取已映射供应商品的成本、库存、SKU 与可用性变化通知。 |

## 安全与一致性规则

- 工具输入不会暴露 `confirm`、`confirmation_id` 或 `idempotency_key`。
- dangerous 操作要求 Agent 先展示精确目标和实质影响，并在调用前取得用户明确确认。
- 必须从之前的 DSers 工具结果复制精确 ID，不能推测店铺、商品、供应商、订单、任务或变体 ID。
- 有版本控制的修改必须使用对应读取工具返回的最新 resource version。
- 上游结果未知的写请求不可自动重试；必须先重新读取当前状态。
- 异步任务创建成功不等于业务完成；使用返回的 `task_type + task_id` 查询 `get_job_status`。

## 结构化错误

工具失败时返回机器可读 JSON，常用错误码包括：

- `UNAUTHORIZED`：token 缺失、无效或已过期。
- `FORBIDDEN`：当前身份无权使用工具或访问资源。
- `INSUFFICIENT_SCOPE`：需要按 `required_scopes` 重新授权。
- `PLAN_RESTRICTION`：当前套餐或 AI Credits 权益不允许操作。
- `VALIDATION_ERROR`：先修正参数再调用。
- `RATE_LIMITED`：等待返回的 rate-limit 时间窗。
- `UPSTREAM_TIMEOUT` 或 `UPSTREAM_ERROR`：只有 `retryable=true` 时才可自动重试。
- `UPSTREAM_UNKNOWN`：写请求可能已到达 DSers；先检查当前状态，禁止自动重试。
