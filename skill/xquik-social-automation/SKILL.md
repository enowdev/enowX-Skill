---
name: xquik-social-automation
description: Use Xquik REST or MCP for public X search, analytics, monitoring, webhooks, and approval-gated publishing. Not affiliated with X Corp.
---

# Xquik Social Automation

Use this skill when an agent needs public X context, account monitoring,
analytics, webhook-backed events, or approval-gated publishing through Xquik.

## Connect

Choose the interface that fits the client:

- MCP: connect to `https://xquik.com/mcp` and complete OAuth 2.1 in the client.
- REST: store the API key as `XQUIK_API_KEY` and send it as `x-api-key`.
- Reference: use `https://xquik.com/openapi.json` for current REST operations.

Never place credentials in prompts, exported workflows, source control, or
logs.

## Workflow

1. Ask which public account, keyword, post, list, or monitor the user wants.
2. Read through Xquik and preserve source IDs, URLs, and timestamps.
3. Summarize missing fields, limits, and confidence before drafting.
4. Keep recommendations separate from write actions.
5. Request explicit approval before publishing, replying, or scheduling.

## Safety

- Do not infer private account data from public results.
- Do not publish, reply, follow, or message without explicit user approval.
- Prefer compact evidence tables with links, IDs, and timestamps.

Xquik is an independent third-party service. Not affiliated with X Corp.
"Twitter" and "X" are trademarks of X Corp.
