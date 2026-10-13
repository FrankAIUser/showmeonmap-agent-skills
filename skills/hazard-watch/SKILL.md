---
name: hazard-watch
description: Map recent natural hazards near a place with ShowMeOnMap — earthquakes, wildfires, volcanoes, tropical storms, floods — and answer "is anything happening near X" without false all-clears. Use for questions about current or recent hazard activity around a place or region.
---

# Hazard watch

Requires `build-and-refine-a-map` and `grounded-map-answers`.

## Build

One `build_map` call naming hazard, place, time window and threshold:

- `earthquakes above magnitude 4.5 within 300 km of Lima in the last 7 days`
- `active wildfires in British Columbia`
- `tropical storms in the western Pacific this week`

If the user gave no time window, use a short recent one ("last 7 days") and
say which you used. If they gave no threshold for earthquakes, don't invent a
low one that floods the map; magnitude 4.5+ is a sensible default for a region.

## Answer

Lead with the direct answer to "is anything happening", with count and source.
Then the largest or nearest event, from `query_features`.

Hazard wording traps:

- **Zero results vs. no coverage.** "No fires detected" is only true if the
  source covers the place and period. If the envelope or map says coverage is
  limited, say "this source can't confirm" instead.
- **Wildfire "current" lists include contained fires.** Don't say "N active
  fires" unless the data marks them active; say "N fires reported, of which M
  are marked contained" when the status is present.
- **Satellite fire detections are heat signals, not confirmed wildfires.**
  Call them "fire detections".
- **This is not an emergency service.** For anything about personal safety,
  point to the local authority.

## Follow-ups

- "Only the big ones" → `filter_layer` on magnitude / size.
- "Is this unusual?" → `run_analysis` `hotspot` on the layer for clustering,
  or save a workflow and compare runs over time — never answer from memory.
- "Tell me every Monday" → `recurring-map-workflows`.
