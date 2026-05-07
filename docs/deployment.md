# Hosted Deployment Notes

This public repository documents the DSers-hosted remote MCP server. It does not include backend source code or production deployment scripts.

## Production Endpoint

```text
https://mcp.dsers.com/dropshipping/mcp
```

The production endpoint is managed by DSers. Users and reviewers should not create new endpoints or change the path.

## Runtime Configuration Categories

Self-hosted or review deployments of the backend require these configuration categories. Do not commit secrets to this repository.

| Variable | Required | Purpose |
| --- | --- | --- |
| `OAUTH_ENCRYPTION_KEY` | required | AES-GCM wrapper-token key. Must be identical across all replicas behind a load balancer. |
| `PUBLIC_MCP_RESOURCE_URL` | optional | Full public MCP resource URL when the request host differs from the public resource URL. |
| `MCP_ALLOWED_ORIGINS` | optional | Comma-separated browser origins allowed for MCP CORS requests. Same-origin and non-browser requests are accepted by default. |
| `DSERS_OAUTH_BASE` | optional | DSers OAuth base URL override for staging or test environments. |
| `DSERS_BASE_URL` | optional | DSers BFF base URL override for staging or test environments. |
| `STATS_PASSWORD` | recommended | Password gate for admin stats, if that endpoint is enabled. |
| `UPSTASH_REDIS_REST_URL` / `UPSTASH_REDIS_REST_TOKEN` | optional | Aggregate analytics backend. |
| `LOG_LEVEL` | optional | Runtime log threshold. |

## ChatGPT Review Deployment

The current ChatGPT App submission target is data-only:

- `CHATGPT_APP_UI=off`
- no widget resource
- no `dsers_app_render` tool
- normal MCP text / JSON tool results

If DSers tests through Cloudflare Workers before production submission, the Worker must be reachable over public HTTPS and must use the same MCP path:

```text
https://<worker-subdomain>.workers.dev/dropshipping/mcp
```

Set secrets through the deployment platform, not in this repository. At minimum, the Worker must have `OAUTH_ENCRYPTION_KEY` before ChatGPT is connected.

## Verification

Before review or release:

```bash
curl -s https://mcp.dsers.com/health
curl -i https://mcp.dsers.com/dropshipping/mcp
```

Expected behavior:

- `/health` returns the current service version.
- unauthenticated `/dropshipping/mcp` responds with OAuth discovery information.
- ChatGPT OAuth completes without API keys, cookies, MFA, SMS, email challenge, VPN, or manual token copying.
