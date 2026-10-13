---
name: hazard-watch
description: Map recent natural hazards near a place with ShowMeOnMap — earthquakes, wildfires, volcanoes, tropical storms, floods — and answer "is anything happening near X" without false all-clears. Use for questions about current or recent hazard activity around a place or region.
---

# Hazard watch

Requires `build-and-refine-a-map` and `grounded-map-answers`.

## Build

One `build_map` call naming hazard, place, time window and threshold:

- `earthquakes above magnitude 4.5 within 300 km of Lima in the last 7 days`
- `active wildfires near Vancouver, British Columbia`
- `tropical storms in the western Pacific this week`

Time: keep the user's words ("right now", "this week"). If they gave none,
use a short recent window ("in the last 7 days") and say which you used.
Thresholds: if they gave none, let the server's default stand and report it
from the layer subtitle; don't invent a low one that floods the map.

## Answer "near X"

The map may cover a whole region (a province, a state). For "near X":

1. Query each hazard layer with `withinDistance` around the place
   (`center: [lng, lat]`, `meters`) — free.
2. Answer for that radius and state it ("within 150 km of Vancouver").
3. Region-wide counts are context, labelled as such ("across BC: 68 fires").

Lead with the direct answer, its count and source. Then the nearest and the
largest events. Distances you compute from coordinates are "about … in a
straight line".

## Wording traps

- **Zero results vs. no coverage.** "No fires detected" is only true if the
  source covers the place and period. Features present around the empty area
  show coverage; if you can't tell, say "this source can't confirm".
- **Agency status words.** Quote them as given ("Under Control", "Being Held",
  "Out of Control"). Don't count everything in a "current" list as active —
  official lists often include fires that are held or under control.
- **Satellite fire points are heat detections, not confirmed wildfires** —
  call them "fire detections" even if the layer title says "active wildfires".
  Mention the data lag the insight evidence gives.
- **Not an emergency service.** For anything about personal safety, point to
  the responsible agency (use the agency link in the data when present) and
  local emergency authorities.

## Follow-ups

- "Only the big ones" → `filter_layer` on magnitude / size (check the count
  free with `query_features` first).
- "Is this unusual?" → `run_analysis` `hotspot` for clustering, or a saved
  workflow compared over time — never answer from memory.
- "Check this every Monday" → `recurring-map-workflows` (runs are on demand).
