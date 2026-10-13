---
name: connect-showmeonmap
description: Connect an agent to the ShowMeOnMap MCP server and recover from setup errors. Use on first use, when build_map or other ShowMeOnMap tools are missing, or when a call fails with a sign-in, API-key, rate-limit or credit error.
---

# Connect ShowMeOnMap

## Check first

If tools named `build_map`, `mutate_map` and `get_state` are already available,
the connection works — skip to `build-and-refine-a-map`.

## Connect

| Host | How |
|---|---|
| claude.ai / Claude Desktop | Settings → Connectors → Add custom connector → `https://showmeonmap.com/api/mcp`. Claude asks the user to sign in the first time a map is built. |
| Claude Code | `claude mcp add --transport http showmeonmap https://showmeonmap.com/api/mcp`, then `/mcp` to sign in. Installing this plugin does the same. |
| Any other MCP host | Remote URL `https://showmeonmap.com/api/mcp` with header `Authorization: Bearer smo_live_…`, or the stdio package `npx -y @showmeonmap/mcp` with `SHOWMEONMAP_BASE_URL=https://showmeonmap.com` and `SHOWMEONMAP_API_KEY=smo_live_…`. |

API keys are created by the user at showmeonmap.com → account menu → API
keys. Never ask the user to paste a key into the chat; tell them where it goes
in their host's config.

## Errors

| Symptom | What it means | Tell the user |
|---|---|---|
| 401 / sign-in prompt | No valid OAuth token or key | Sign in again via the connector, or check the key in config |
| 402 / insufficient credits | The account has no credits left | Credit packs are at showmeonmap.com/pricing; reads and exports still work |
| 429 | More than 30 requests a minute | Wait a minute; batch the request into one `build_map` call |
| `Unknown map_id` | The map was built in another session or expired from the cache | Build it again with `build_map`; old maps stay viewable at their `/m/` link |

## Costs

Building or changing a map spends the user's credits. Reading state
(`get_state`, `query_features`, `list_workflows`) and exporting are free.
Failed operations are refunded automatically.
