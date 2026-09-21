# Privacy and Security

The DSers Official MCP Server is hosted by DSers. This public repository contains documentation and metadata only; it does not contain production credentials or customer data.

Hosted privacy policy:

```text
https://mcp.dsers.com/privacy-policy
```

## Data processed

Depending on the authorized account, selected tool, and supplied arguments, the service may process:

- OAuth identity, roles, scopes, DSers user identifiers, and a short-lived request credential needed to call DSers services.
- Account, plan, AI Credit, billing, invoice, store, application, and platform-setting data.
- Supplier products, pre-publish products, managed store products, variants, images, prices, inventory, Mapping, and Publishing data.
- Orders, addresses, billing records, supplier orders, shipping methods, packages, tracking numbers, and notification state.
- Operational data such as tool name, status, latency, request identifiers, rate-limit state, quota usage, and audit metadata.

## Storage and logging

The MCP BFF is stateless for protocol sessions, but some operational and workflow data is persisted where the current service requires it:

- tool audit records may be stored in DSers MySQL infrastructure;
- asynchronous task state may be stored so owner-scoped jobs can be polled;
- Redis may be used for quotas, rate limits, locks, and short-lived caches;
- logs, metrics, and traces may contain minimized operational metadata.

Generic audit serialization redacts tokens, secrets, street lines, contacts, phone numbers, email addresses, tax data, company data, and passport fields. Address-related tools use summary-level audit policy.

Retention periods are governed by the deployed DSers services and operational policy. This repository does not promise fixed token, audit, analytics, cache, or task-retention periods.

## Connected services

The MCP service communicates with DSers account, plan, product, settings, order, tracking, and related services. A user-requested operation may also affect a connected sales channel or supplier application, for example Shopify publishing, fulfillment, or buyer tracking notification.

## Safety

- Use OAuth; never submit passwords, MFA codes, API keys, cookies, or manually copied tokens.
- Read current state and use exact DSers identifiers before any mutation.
- Obtain explicit user confirmation immediately before a dangerous tool call.
- Do not automatically retry a write reported as `UPSTREAM_UNKNOWN`.
- Re-read versioned resources before a later write and use the latest resource version.

## Contact

For privacy or security questions, contact `zhaohaoduo@dsers.com`.
