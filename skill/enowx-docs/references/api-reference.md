# enowX API — full endpoint reference

Generated from the live `/api/docs` catalog, which mirrors the running
gateway. When a detail matters for the current version, re-fetch
`GET /api/docs`.

- **Base URL:** `http://localhost:1430`
- **OpenAI-compatible:** `/v1`  ·  **Anthropic-compatible:** `/anthropic`
- **Auth:** When at least one gateway API key exists, send Authorization: Bearer <key> to /v1 and /anthropic. With no keys, the gateway is open (localhost).
- **Response envelope:** Management /api responses are wrapped as {"data": ...} on success or {"error": "..."} on failure.

## Inference

OpenAI- and Anthropic-compatible inference, routed to a provider by model id.

### `POST /v1/chat/completions`
OpenAI chat completions (streaming or JSON). Model id like 'codebuddy/...' or 'kiro/...' selects the provider.

| param | in | description |
|---|---|---|
| `model` | body | model id |
| `messages` | body | chat messages |
| `stream` | body | stream SSE |

### `GET /v1/models`
OpenAI-standard model list ({object:list, data:[{id,object:model,owned_by}]}) with the same prefixed ids chat completions accepts — for external OpenAI-compatible clients.

### `POST /anthropic/v1/messages`
Anthropic Messages API; decoded to the internal request and streamed back as Anthropic SSE.

### `GET /health`
Liveness check; returns {"status":"ok"}.

## Providers

Registered upstream providers and their display metadata.

### `GET /api/providers`
List registered providers (name, label, icon, caps).

## Accounts

The credential pool: per-provider accounts used to serve requests.

### `GET /api/accounts`
List accounts (never returns secret values).

| param | in | description |
|---|---|---|
| `provider` | query | filter by provider (optional) |

### `POST /api/accounts`
Add an account.

| param | in | description |
|---|---|---|
| `provider` | body | provider name |
| `label` | body | display label |
| `secret` | body | single-token credential |
| `creds` | body | multi-field credentials |

### `PATCH /api/accounts/{id}/status`
Set upstream status.

| param | in | description |
|---|---|---|
| `id` | path | account id |
| `status` | body | active|exhausted|banned |

### `PATCH /api/accounts/{id}/disabled`
Enable/disable an account (skipped by the pool while disabled).

| param | in | description |
|---|---|---|
| `id` | path | account id |
| `disabled` | body | true to disable |

### `GET /api/accounts/{id}/usage`
Credit/quota usage when the provider supports it.

| param | in | description |
|---|---|---|
| `id` | path | account id |

### `POST /api/accounts/{id}/warmup`
Send a real probe request, update status, fetch credit; records a warmup log.

| param | in | description |
|---|---|---|
| `id` | path | account id |

### `DELETE /api/accounts/{id}`
Delete an account.

| param | in | description |
|---|---|---|
| `id` | path | account id |

## Kiro account flows

Provider-specific ways to add a Kiro account.

### `POST /api/accounts/kiro/manual`
Add by pasting the kiro-auth-token.json contents.

| param | in | description |
|---|---|---|
| `json` | body | auth JSON |
| `label` | body | optional |

### `POST /api/accounts/kiro/refresh`
Add by refresh token.

| param | in | description |
|---|---|---|
| `refresh_token` | body | token |
| `region` | body | sso region |

### `POST /api/accounts/kiro/aws/start`
Start AWS device-code login; returns a user code + verification URL.

| param | in | description |
|---|---|---|
| `region` | body | sso region |

### `GET /api/accounts/kiro/aws/poll`
Poll the AWS device login; saves the account when approved.

| param | in | description |
|---|---|---|
| `session` | query | session id |

### `POST /api/accounts/kiro/oauth/start`
Start Google/social OAuth; returns an authorize URL.

### `POST /api/accounts/kiro/oauth/exchange`
Exchange the redirect code for tokens.

| param | in | description |
|---|---|---|
| `session` | body | session id |
| `code` | body | auth code |

## Local credentials

Import accounts from credentials installed tools wrote to disk (loopback only).

### `GET /api/local-sources`
Scan for detectable local credential files.

### `POST /api/local-sources/import`
Import a detected source as an account.

| param | in | description |
|---|---|---|
| `provider` | body | provider |
| `target` | body | source label |

## API keys

Gateway keys that protect /v1 and /anthropic.

### `GET /api/keys`
List gateway keys (re-viewable, with limits + usage).

### `POST /api/keys`
Create a gateway key with optional limits.

| param | in | description |
|---|---|---|
| `label` | body | optional |
| `token_limit` | body | total tokens allowed; 0 = unlimited |
| `max_concurrent` | body | simultaneous requests; 0 = unlimited |
| `expires_in_days` | body | expiry in days; 0 = never |

### `DELETE /api/keys/{id}`
Delete a gateway key.

| param | in | description |
|---|---|---|
| `id` | path | key id |

## Proxy pool

Outbound proxies that upstream provider requests can be routed through. Add proxies in any format (scheme URLs, host:port:user:pass, ip:port, bulk paste); routing is controlled by the settings (enabled, mode, per-provider whitelist). The pool syncs to the cloud like accounts.

### `GET /api/proxies`
List the pool (passwords stripped).

### `POST /api/proxies`
Add one or many proxies (any format). Returns {added, errors}.

| param | in | description |
|---|---|---|
| `text` | body | proxies, one per line for bulk |

### `DELETE /api/proxies/{id}`
Delete a proxy.

| param | in | description |
|---|---|---|
| `id` | path | proxy id |

### `PATCH /api/proxies/{id}/enabled`
Enable/disable a proxy.

| param | in | description |
|---|---|---|
| `id` | path | proxy id |
| `enabled` | body | true to enable |

### `POST /api/proxies/{id}/test`
Probe a proxy (fetches ipify through it); records status + latency.

| param | in | description |
|---|---|---|
| `id` | path | proxy id |

### `GET /api/proxies/settings`
Routing config: {enabled, mode, providers}.

### `PUT /api/proxies/settings`
Update routing config.

| param | in | description |
|---|---|---|
| `enabled` | body | route through the pool |
| `mode` | body | rotate|random|sticky |
| `providers` | body | provider names to proxy ([] = all) |

## Requests & stats

Served request history and usage statistics.

### `GET /api/requests`
Recent request log rows (incl. proxy_used + account_label per request; no request/response bodies).

| param | in | description |
|---|---|---|
| `limit` | query | max rows |

### `DELETE /api/requests`
Clear all request logs.

### `GET /api/requests/summary`
Today's totals (requests, ok, errors, tokens, avg latency).

### `GET /api/requests/series`
Time-bucketed series.

| param | in | description |
|---|---|---|
| `range` | query | daily|7d|30d|all |

### `GET /api/requests/top-models`
Top models today.

| param | in | description |
|---|---|---|
| `limit` | query | max models |

## Warmup logs

History of account warmup probes.

### `GET /api/warmup-logs`
Recent warmup entries (request, response, usage).

| param | in | description |
|---|---|---|
| `limit` | query | max rows |

### `DELETE /api/warmup-logs`
Clear all warmup logs.

## Cloud sync

Two-way sync of local data to the enowxlabs cloud, gated by Discord login. Pilot data type: playlists. The enowx client talks to the cloud server; these endpoints drive it.

### `GET /api/sync/status`
Sync state: configured, enabled, server URL, and cached user (identity/plan).

### `POST /api/sync/login`
Begin Discord login; returns an authorize URL to open + a state to poll.

| param | in | description |
|---|---|---|
| `server_url` | body | cloud base URL (optional if already set) |

### `GET /api/sync/login/poll`
Poll for login completion; stores the sync token when done.

| param | in | description |
|---|---|---|
| `state` | query | state from login |

### `POST /api/sync/logout`
Drop the sync token and disable sync.

### `POST /api/sync/now`
Run a one-off reconcile; returns counts pushed/pulled.

## Dashboard auth

Optional dashboard password. Localhost is trusted without login; remote access (e.g. via a tunnel) needs a session. The terminal and file browser require this when reached remotely.

### `GET /api/auth/status`
Whether a password is set, the request is from localhost, logged in, and authorized.

### `POST /api/auth/setup`
Set the dashboard password the first time (trusted caller only).

| param | in | description |
|---|---|---|
| `password` | body | min 6 chars |

### `POST /api/auth/login`
Exchange the password for a session cookie.

| param | in | description |
|---|---|---|
| `password` | body | dashboard password |

### `POST /api/auth/logout`
Clear the current session.

### `POST /api/auth/change`
Change the password (requires current).

| param | in | description |
|---|---|---|
| `current` | body | current password |
| `new` | body | new password (min 6) |

## Tunnel

Expose the gateway to the public internet via Cloudflare Tunnel. Enabling requires at least one API key (an unauthenticated public gateway would let anyone spend your accounts).

### `GET /api/tunnel/status`
Tunnel state: enabled, mode (quick|named), public url, hostname, logged_in, and binary download progress.

### `POST /api/tunnel/enable`
Start a quick tunnel (random trycloudflare.com URL, no account). Downloads cloudflared on first use.

### `POST /api/tunnel/disable`
Stop the tunnel.

### `POST /api/tunnel/login`
SSE: run cloudflared browser login; streams progress + the authorization URL, then 'done' when the cert is saved.

### `POST /api/tunnel/named`
Create/route/run a named tunnel on your own hostname (requires prior login).

| param | in | description |
|---|---|---|
| `hostname` | body | e.g. enowx.example.com |

## Music

Search YouTube Music for songs and proxy the chosen track's audio for playback.

### `GET /api/music/search`
Search songs; returns {id, title, artist, album, duration, thumbnail}.

| param | in | description |
|---|---|---|
| `q` | query | search query |

### `GET /api/music/stream`
Proxy the best audio-only stream for a video id; forwards Range for seeking.

| param | in | description |
|---|---|---|
| `id` | query | video id from search |

### `GET /api/music/discover`
A shuffled 'for you' feed: biased toward your most-played artists, padded with seed genres. Cold-start uses genres only.

### `GET /api/music/history`
Recently played distinct tracks.

| param | in | description |
|---|---|---|
| `limit` | query | max tracks |

### `POST /api/music/history`
Record a play (feeds Discover).

| param | in | description |
|---|---|---|
| `id` | body | video id |
| `title` | body | title |
| `artist` | body | artist |
| `album` | body | album |

### `DELETE /api/music/history`
Clear all play history.

### `GET /api/music/playlists`
List local playlists (id, name, share_code, track count).

### `POST /api/music/playlists`
Create a local playlist.

| param | in | description |
|---|---|---|
| `name` | body | playlist name |
| `description` | body | optional |

### `GET /api/music/playlists/{id}`
Get a playlist with its tracks.

| param | in | description |
|---|---|---|
| `id` | path | playlist id |

### `DELETE /api/music/playlists/{id}`
Delete a playlist and its tracks.

| param | in | description |
|---|---|---|
| `id` | path | playlist id |

### `POST /api/music/playlists/{id}/tracks`
Add a track to a playlist.

| param | in | description |
|---|---|---|
| `id` | path | playlist id |
| `id` | body | video id |
| `title` | body | title |
| `artist` | body | artist |

### `DELETE /api/music/playlists/{id}/tracks/{videoId}`
Remove a track from a playlist.

| param | in | description |
|---|---|---|
| `id` | path | playlist id |
| `videoId` | path | video id |

### `GET /api/music/playlists/{id}/export`
Export a playlist as a portable JSON document (share/plugin contract).

| param | in | description |
|---|---|---|
| `id` | path | playlist id |

### `POST /api/music/playlists/import`
Import a playlist from an exported JSON document.

| param | in | description |
|---|---|---|
| `name` | body | playlist name |
| `tracks` | body | array of tracks |

## Plugins & marketplace

Manage local plugins (sidecar mini-apps) and the shared marketplace. Your plugin's UI is served at /plugins/{id}/; from a plugin you mostly call the other groups here via enowx.api(...), but these drive the plugin lifecycle itself.

### `GET /api/plugins`
List installed plugins (manifest, running state, port) + detected runtimes.

### `POST /api/plugins`
Scaffold a new plugin folder under ~/.enowx/plugins/{id}/.

| param | in | description |
|---|---|---|
| `id` | body | plugin id (a-z0-9-) |
| `name` | body | display name |
| `runtime` | body | go|python|node|static |
| `starter` | body | include starter code |

### `POST /api/plugins/{id}/start`
Start the sidecar (assigns PORT, proxies its UI).

| param | in | description |
|---|---|---|
| `id` | path | plugin id |

### `POST /api/plugins/{id}/stop`
Stop the sidecar.

| param | in | description |
|---|---|---|
| `id` | path | plugin id |

### `POST /api/plugins/{id}/reveal`
Open the plugin folder in the OS file manager.

| param | in | description |
|---|---|---|
| `id` | path | plugin id |

### `GET /api/plugins/{id}/logs`
Recent sidecar stdout/stderr.

| param | in | description |
|---|---|---|
| `id` | path | plugin id |

### `GET /api/plugins/{id}/icon`
Serve the plugin's icon.

| param | in | description |
|---|---|---|
| `id` | path | plugin id |

### `POST /api/plugins/{id}/icon`
Upload a custom icon (multipart, auto-fit).

| param | in | description |
|---|---|---|
| `id` | path | plugin id |

### `DELETE /api/plugins/{id}`
Delete a plugin and its folder.

| param | in | description |
|---|---|---|
| `id` | path | plugin id |

### `GET /api/market/plugins`
Browse published marketplace plugins.

| param | in | description |
|---|---|---|
| `q` | query | search query (optional) |

### `POST /api/market/publish`
Bundle a local plugin + publish it (security-scanned). Returns {status: approved|rejected|pending, reason}.

| param | in | description |
|---|---|---|
| `id` | body | local plugin id |

### `POST /api/market/install/{id}`
Download + install a published plugin into ~/.enowx/plugins/.

| param | in | description |
|---|---|---|
| `id` | path | marketplace plugin id |

## OTP

Rent disposable SMS numbers for OTP codes via Warpize. Bring your own Warpize key (wz_live_…) — get one at https://warpize.com. Your key is stored encrypted; enowX proxies to Warpize and never sees the codes.

### `GET /api/otp/config`
Whether a Warpize key is set (+ a masked preview).

### `POST /api/otp/config`
Save/replace your Warpize API key.

| param | in | description |
|---|---|---|
| `api_key` | body | wz_live_… key |

### `DELETE /api/otp/config`
Remove the stored Warpize key.

### `GET /api/otp/balance`
Your Warpize account balance.

### `GET /api/otp/services`
Available services + countries + prices.

| param | in | description |
|---|---|---|
| `q` | query | search (optional) |

### `POST /api/otp/rent`
Rent a number for a service/country.

| param | in | description |
|---|---|---|
| `service` | body | service id |
| `country` | body | country id |

### `GET /api/otp/orders/{id}`
Poll a rented order for its SMS code.

| param | in | description |
|---|---|---|
| `id` | path | order id |

### `POST /api/otp/orders/{id}/cancel`
Cancel an order.

| param | in | description |
|---|---|---|
| `id` | path | order id |

## Skills

Community Skill registry. Browse + publish Skills; every upload is security-scanned then committed to the enowdev/enowX-Skill GitHub repo. Install with the CLI: `enx skill install <slug>` (into ./.agents/skill/<slug>) or `enx skill install <slug> -g` (into ~/.agents/skill/<slug>).

### `GET /api/registry`
Browse published skills.

| param | in | description |
|---|---|---|
| `kind` | query | skill (or mcp) |
| `q` | query | search (optional) |

### `GET /api/registry/{id}`
One skill's detail + its GitHub folder URL (counts a download).

| param | in | description |
|---|---|---|
| `id` | path | registry item id |

### `POST /api/registry/publish`
Publish a skill. Send its files (auto-read from the picked folder); scanned, then committed. Returns {status: approved|rejected, reason}.

| param | in | description |
|---|---|---|
| `kind` | body | skill |
| `name` | body | skill name |
| `description` | body | auto-detected from SKILL.md |
| `version` | body | version |
| `files` | body | [{path, content(base64)}] |

## System

Gateway info, process debug, and local tools (loopback only where noted).

### `GET /api/settings`
Version, host, port, runtime dir, uptime.

### `GET /api/debug`
Process CPU/RSS + Go runtime + build info.

### `GET /api/files`
List a directory (loopback only).

| param | in | description |
|---|---|---|
| `path` | query | directory (defaults to home) |

### `GET /api/files/read`
Read a text file, capped (loopback only).

| param | in | description |
|---|---|---|
| `path` | query | file path |

### `GET /api/files/raw`
Stream raw file bytes, e.g. for images (loopback only).

| param | in | description |
|---|---|---|
| `path` | query | file path |

### `GET /api/terminal`
WebSocket PTY shell, keyed by ?id= so a session persists across reconnects (scrollback replayed on reattach). Loopback only.

| param | in | description |
|---|---|---|
| `id` | query | terminal/session id (persists the shell) |

### `GET /api/docs`
This endpoint catalog (machine-readable).
