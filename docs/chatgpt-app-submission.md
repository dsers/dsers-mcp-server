# ChatGPT App Submission Notes

## Submission shape

- Display name: DSers
- Remote endpoint: `https://ai.dsers.com/mcp`
- Transport: Streamable HTTP
- Authentication: OAuth 2.1 with PKCE
- Surface: data-only MCP tools
- Widget or iframe resource: none
- MCP prompts or resources: none
- Current tool inventory: 60 tools

ChatGPT receives standard MCP tool results and writes the user-facing response in chat.

## Source files

- [`manifest.json`](../manifest.json) contains the public app metadata and the same 60-tool inventory.
- [`chatgpt-app-submission.json`](../chatgpt-app-submission.json) contains tool annotations, justifications, positive test cases, and negative test cases.
- [`docs/tools.md`](tools.md) is the human-readable tool catalog.
- [`docs/auth.md`](auth.md) describes discovery, roles, scopes, and failure handling.
- [`docs/privacy.md`](privacy.md) describes current data processing and storage categories.

## Tool annotations

The submission classifies every tool using the current BFF risk policy:

- 34 read tools use `readOnlyHint=true` and `destructiveHint=false`.
- 9 write tools use `readOnlyHint=false` and `destructiveHint=false`.
- 17 dangerous tools use `readOnlyHint=false` and `destructiveHint=true`.

Tools that can directly change connected commerce-platform state or communicate with a buyer also use `openWorldHint=true`. Other tools remain bounded to DSers account or private workflow state.

## Review account

The review account should contain enough non-sensitive sample data to exercise the selected review cases, such as a connected test store, visible supplier applications, supplier-product candidates, pre-publish products, managed products, orders, or packages. Tests should not require reviewers to enter API keys, cookies, internal network access, or manually copied tokens.

## Required safety behavior

- ChatGPT must use exact IDs returned by DSers tools and must not invent identifiers.
- Dangerous operations require a current preview and explicit user confirmation immediately before invocation.
- Tool schemas do not accept `confirm`, `confirmation_id`, or `idempotency_key`.
- Versioned writes use the latest resource version.
- `UPSTREAM_UNKNOWN` writes are never automatically retried.
- Asynchronous creation is followed through `get_job_status` using the returned `task_type + task_id`.
- Credentials and sensitive customer data are not echoed in responses.

## Maintenance

Update this repository whenever registered tool names, descriptions, annotations, scopes, endpoint URLs, privacy categories, or review workflows change. The three inventories in the BFF catalog, `manifest.json`, and `chatgpt-app-submission.json` must remain identical.
