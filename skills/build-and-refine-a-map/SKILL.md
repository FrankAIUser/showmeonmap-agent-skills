---
name: build-and-refine-a-map
description: Build a data-driven map with ShowMeOnMap in one call, then change it in place across follow-up turns. Use for any request whose answer is data on a map (hazards, statistics by region, infrastructure, transit, parks, points of interest) and for every follow-up such as filter, zoom, restyle, add or remove a layer.
---

# Build and refine a map

## 1. Build once, with the whole request

Call `build_map` **once** with everything the user said: subject, place, time
window, thresholds. Keep the user's own time words ("this month", "right now",
"past year") — the server interprets them. Only turn a vague word into a
window when it has none ("lately" → "in the last 30 days").

| User says | `query` |
|---|---|
| "big quakes near Japan this month" | `earthquakes above magnitude 5 near Japan this month` |
| "GDP per person in Europe" | `GDP per capita across Europe` |
| "fires near Vancouver right now" | `active wildfires near Vancouver, British Columbia` |

The server applies place, time and thresholds itself. Do **not** follow up with
`set_camera`, `filter_layer` or `set_time` to re-apply something already in the
query — that spends credits and can fight the server's own framing.

**Ambiguous places.** If a name has no dominant meaning ("Springfield",
"Victoria"), ask which one **before** building — building the wrong place costs
a credit and a turn. If one meaning clearly dominates ("Vancouver" → BC,
"Lima" → Peru), build it and state the assumption in your answer. When asking,
you may list likely candidates from general knowledge; the user picks.

**Defaults the server adds.** When the user gives no threshold, the server may
apply one (e.g. earthquakes M4.5+). Read the layer subtitle after building and
tell the user what was applied. If the default would starve the question (a
cluster analysis on a handful of events), put an explicit threshold in the
query up front instead ("earthquakes M2.5+ in California in the past year").

## 2. Read the envelope before saying anything

Every tool returns the same envelope. Act on it in this order:

| Field | Meaning | Do |
|---|---|---|
| `clarification` | The server needs a choice | Ask the user the question; offer the `candidates` |
| `outOfScope` | The instruction doesn't fit this map | Build a new map with `build_map` |
| `ok: false` | Typed failure | Say what failed; don't retry the same call |
| `structured.mapPlan.insights` | Computed headline + evidence ("3 earthquakes found", source, data lag) | Primary grounded summary — quote it |
| `structured.mapPlan.layers[].subtitle` | Filter, period and licence actually applied | State what was applied |
| `contextAnswer` | Computed one-line answer, when present ("kept 37 of 150 features") | Quote it |
| `shareable_url` | Link to the live map | Always give it to the user |
| `next_actions` | Generic follow-up hints | Offer only ones that fit the data and aren't already true |

Then write the answer following `grounded-map-answers`.

**Untyped server errors** (HTTP 5xx, an HTML error page instead of an
envelope): retry **once**. If it fails again, stop, tell the user the service
returned an error, and suggest checking their credit balance — don't loop.

## 3. Refine in place — same `map_id`, never rebuild

A follow-up changes the existing map. Keep the `map_id` from the last call.

| Follow-up | Tool |
|---|---|
| "only the ones above X", "just the open ones" | `filter_layer` (or `mutate_map` with the sentence) |
| "zoom to Osaka", "just this neighbourhood" | `focus_area` with a named place, street corridor or intersection — no coordinates needed |
| "make it red", "bigger dots" | `restyle_layer` on the existing layer id |
| "also show volcanoes" | `add_layer` |
| "remove the roads" | `remove_layer` |
| "go back" | `undo` |
| anything else in plain words | `mutate_map` with the instruction |

Before a filter:

- **Learn the field names** from a free `query_features` call (feature
  properties) — don't guess them.
- **Check the result for free first.** Run the same predicate through
  `query_features`. If nothing matches, tell the user ("none of the 3 are
  deeper than 50 km — the deepest is 42 km") and ask whether to apply the
  filter anyway, rather than spending a credit on an empty map.

Rules that keep maps clean:

- **Reuse layer ids.** Restyle or filter the layer that exists; never add a
  second copy of the same data to change how it looks.
- **Leave the basemap alone** unless the user asks to change it.
- **Don't add filters, selections or annotations the user didn't ask for.**
- Layer ids are in the envelope; call `get_state` (free) only when you've lost
  track of them.

## 4. Predicates

`filter_layer` and `query_features` take a predicate tree. Common shapes:

```
{ op: 'eq' | 'ne' | 'gt' | 'gte' | 'lt' | 'lte', field, value }
{ op: 'in' | 'nin', field, values: [...] }
{ op: 'between', field, min, max }
{ op: 'withinDistance', center: [lng, lat], meters }
{ op: 'within', geometry: { type: 'Polygon', coordinates: [...] } }
{ op: 'bbox', box: [west, south, east, north] }
{ op: 'before' | 'after', field, value: 'YYYY-MM-DD' }
{ op: 'and' | 'or', children: [...] }      // { op: 'and', children: [] } matches all
{ op: 'not', child: <predicate> }
```

The full grammar is in the MCP resource `showmeonmap://docs/workspace-ops-spec`
when your client can read resources.

**"Near X".** The server may return a whole region for "near X". To answer
"near", query with `withinDistance` around the place and say the radius you
used ("within 150 km").

## 5. When the server can't do it

If a request is out of reach after a fair attempt, say so plainly and call
`report_blocker` with what you tried. It changes nothing on the map and helps
the server grow the missing capability.
