---
name: enowx-docs
description: This skill should be used when the user asks how to use the enowX AI gateway — its OpenAI/Anthropic-compatible inference API, provider accounts, gateway API keys, proxy pool, cloud sync, OTP numbers, the Skill registry, or any enowX endpoint. It explains how to discover the live, machine-readable API catalog and how each area works.
---

# enowX Docs

## Overview

enowX is a local-first AI gateway. It exposes an **OpenAI-compatible** API at
`/v1` and an **Anthropic-compatible** API at `/anthropic`, and routes each
request to an upstream provider account based on the model id. Everything else
(providers, accounts, keys, proxies, sync, OTP, skills) is managed through its
`/api/*` endpoints and a desktop-style dashboard.

Use this skill to answer "how do I … in enowX" and to find the exact endpoint.

## The live API catalog is the source of truth

enowX serves its own, always-current endpoint catalog:

```
GET /api/docs
```

It returns JSON: `{ version, overview{ base_url, openai_base, anthropic_base,
auth }, groups[ { name, desc, endpoints[ { method, path, desc, params } ] } ] }`.

**Always fetch `/api/docs` first** rather than relying on memory — the catalog
reflects the running version. The default local base URL is `http://localhost:1430`
(the dashboard/API port). Example:

```bash
curl -s http://localhost:1430/api/docs | jq '.data.groups[].name'
```

## Auth

- With **no gateway API keys**, the gateway is open on localhost.
- Once **any** gateway key exists, send `Authorization: Bearer <key>` to `/v1`
  and `/anthropic`. Manage keys under the **API keys** endpoints.

## Making an inference request

OpenAI-compatible (streaming or JSON). The `model` id selects the provider, e.g.
`codebuddy/…`, `kiro/…`:

```bash
curl -s http://localhost:1430/v1/chat/completions \
  -H "Authorization: Bearer <key>" \
  -H "Content-Type: application/json" \
  -d '{"model":"codebuddy/…","messages":[{"role":"user","content":"hi"}]}'
```

Anthropic-compatible requests go to `/anthropic`. Use `GET /v1/models` to list
what's available.

## Endpoint areas (groups)

Each area is a group in `/api/docs`. Fetch the catalog for the exact paths +
params; this is the map:

- **Inference** — `/v1/*`, `/anthropic/*` chat + models.
- **Providers** — registered upstream providers + metadata.
- **Accounts** — the credential pool (add/list/status/warmup/delete). Includes
  **Kiro account flows** (manual token, refresh token, AWS device login, OAuth).
- **Local credentials** — import accounts other installed tools wrote to disk.
- **API keys** — gateway keys protecting `/v1` and `/anthropic`.
- **Proxy pool** — outbound proxies upstream requests can be routed through.
- **Requests & stats** — served-request history + usage summary/series.
- **Warmup logs** — history of account warmup probes.
- **Cloud sync** — two-way sync to the enowxlabs cloud (Discord login).
- **Dashboard auth** — optional dashboard password (localhost trusted).
- **Tunnel** — expose the gateway publicly via Cloudflare Tunnel.
- **Music** — search YouTube Music + proxy audio.
- **Plugins & marketplace** — local sidecar plugins + the shared marketplace.
- **OTP** — rent disposable SMS numbers via Warpize (see `references/otp.md`).
- **Skills** — the community Skill registry (see `references/skills.md`).
- **System** — gateway info, debug, and local file/terminal tools (loopback).

## How to help

1. Fetch `GET /api/docs` and read the relevant group.
2. Point the user at the exact `method + path` and its params.
3. For inference, remind them the `model` id chooses the provider, and about the
   Bearer-key rule.
4. For deeper topics, read the matching file in `references/`.
