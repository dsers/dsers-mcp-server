# DSers MCP User Guide

## 1. Overview

DSers MCP is a hosted Streamable HTTP service that lets OAuth-authorized AI clients work with DSers account, store, supplier-product, publishing, Mapping, order, billing, and tracking workflows.

This repository documents the hosted service. It is not a self-hosting package and does not contain backend source code.

## 2. Connect and authorize

Add `https://ai.dsers.com/mcp` to a remote HTTP MCP client. On first connection, use the client's Sign in, Connect, Authorize, or Login action and complete DSers OAuth in the browser.

Configure only the server URL. Never paste passwords, API keys, cookies, store tokens, or manually copied Authorization headers.

## 3. Understand what is available

The server exposes 60 tools and no MCP prompts or resources. Tools are grouped into:

- account, plan, billing, and navigation
- stores and platform settings
- supplier discovery
- pre-publish products and Pricing Rules
- managed products and Mapping
- publishing and asynchronous jobs
- orders and fulfillment
- packages, tracking, and supplier-product changes

See [Tools](tools.md) for the current names and risk classifications.

## 4. Use evidence-driven workflows

Most DSers operations are multi-step. Read first, copy exact identifiers, show the proposed effect, then write only when the user has requested it.

Common evidence sources include:

- `list_stores` for store, sales-channel, and supplier-platform IDs
- `list_supplier_search_filters` for supported supplier filters
- `get_pre_publish_product` for the current editable state and resource version
- `get_store_product_mapping` for current Mapping relationships
- `list_orders` and `get_order` for exact order, supplier, store, and tab context
- `get_job_status` for asynchronous completion

Do not infer identifiers from names, URLs, or prior knowledge when a DSers tool can provide the canonical ID.

## 5. Confirm dangerous actions

Dangerous tools may publish, delete, remap, edit products, change orders, create payment flows, cancel supplier orders, or send fulfillment/notification state to a connected platform.

Before calling one:

1. Read the latest resource state.
2. Show the exact resource IDs, target stores or orders, and material effect.
3. Explain irreversible or externally visible consequences.
4. Obtain explicit user confirmation immediately before the call.

Confirmation is conversational. Current tool schemas do not accept `confirm`, `confirmation_id`, or `idempotency_key`.

## 6. Handle versions and asynchronous work

For versioned resources, pass the latest resource version returned by the read tool. If a stale-state error occurs, re-read and present the new state instead of forcing the write.

When a write creates an asynchronous task, preserve both `task_type` and `task_id` and use them with `get_job_status`. A created task is not proof that every product, store, order, or package has completed successfully.

## 7. Handle errors safely

Tool errors are structured JSON with a stable `code`, message, `retryable` flag, and optional recovery fields.

- Reauthorize on `UNAUTHORIZED` or `INSUFFICIENT_SCOPE`.
- Explain plan or AI Credit requirements on `PLAN_RESTRICTION`.
- Respect the returned wait period on `RATE_LIMITED`.
- Correct arguments on `VALIDATION_ERROR`.
- Retry upstream failures only when `retryable=true`.
- Never automatically retry `UPSTREAM_UNKNOWN`; inspect the resource first.

## 8. Example workflows

See [Examples](examples.md) for supplier discovery, pre-publish editing, Publishing, Mapping, order diagnosis, and deletion sequences.

## 9. Privacy and support

See [Privacy and Security](privacy.md) before handling customer, order, address, billing, or tracking data. Use public GitHub issues only for non-sensitive documentation and connection questions. Send security or account-specific reports to `zhaohaoduo@dsers.com`.
