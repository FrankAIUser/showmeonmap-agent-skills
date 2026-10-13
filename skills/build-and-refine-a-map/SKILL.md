---
name: build-and-refine-a-map
description: Build a data-driven map with ShowMeOnMap in one call, then change it in place across follow-up turns. Use for any request whose answer is data on a map (hazards, statistics by region, infrastructure, transit, parks, points of interest) and for every follow-up such as filter, zoom, restyle, add or remove a layer.
---

# Build and refine a map

## 1. Build once, with the whole request

Call `build_map` **once** with everything the user said: subject, place, time
window, thresholds.

| User says | `query` |
|---|---|
| "big quakes near Japan lately" | `earthquakes above magnitude 5 near Japan in the last 30 days` |
| "GDP per person in Europe" | `GDP per capita across Europe` |
| "fires in California right now" | `active wildfires in California` |

The server applies place, time and thresholds itself. Do **not** follow up with
`set_camera`, `filter_layer` or `set_time` to re-apply something already in the
query — that spends credits and can fight the server's own framing.

If the place name is ambiguous ("Springfield", "Victoria"), ask which one
**before** building. Building the wrong place costs a credit and a turn.

## 2. Read the envelope before saying anything

Every tool returns the same envelope. Act on it in this order:

| Field | Meaning | Do |
|---|---|---|
| `clarification` | The server needs a choice | Ask the user the question; offer the `candidates` |
| `outOfScope` | The instruction doesn't fit this map | Build a new map with `build_map` |
| `ok: false` | Typed failure | Say what failed; don't retry the same call blindly |
| `contextAnswer` | A computed one-line answer ("kept 37 of 150 features") | Quote it — it is grounded |
| `shareable_url` | Link to the live map | Always give it to the user |
| `next_actions` | Typed follow-up hints | Offer one or two as suggestions |

Then write the answer following `grounded-map-answers`.

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

Rules that keep maps clean:

- **Reuse layer ids.** Restyle or filter the layer that exists; never add a
  second copy of the same data to change how it looks.
- **Leave the basemap alone** unless the user asks to change it.
- **Don't add filters, selections or annotations the user didn't ask for.**
- **Exact operation shapes:** read the MCP resource
  `showmeonmap://docs/workspace-ops-spec` instead of guessing predicate syntax.
- Call `get_state` (free) when you've lost track of which layers exist.

## 4. When the server can't do it

If a request is out of reach after a fair attempt, say so plainly and call
`report_blocker` with what you tried. It changes nothing on the map and helps
the server grow the missing capability.
