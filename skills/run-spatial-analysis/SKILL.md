---
name: run-spatial-analysis
description: Run server-computed spatial analyses on a ShowMeOnMap map — hot spots (Getis-Ord Gi*), per-zone aggregation, US Census enrichment, spatial join, nearest neighbour, merge and dissolve — and correlate two layers. Use when the user asks where something clusters, totals per area, demographics around features, what is closest, or how two variables relate.
---

# Run a spatial analysis

All analyses run on the server with fixed, documented methods. Never compute
statistics yourself from feature lists and present them as analysis results.

## Pick the analysis

| The user wants | `run_analysis` `analysis` | Key inputs |
|---|---|---|
| "Where do these cluster?" / "hot spots" | `hotspot` | `layerId`; `valueField` for a numeric field, omit it to test point density |
| "How many per district / per hexagon?" | `zonal` | point `layerId` + `zoneLayerId`, or `zones: {kind:'h3'}` |
| "Income / rent / home value around these" (US) | `enrich` | `layerId` + `indicator` (e.g. `median_household_income`) |
| "Which district is each point in?" | `join` | point `layerId` + polygon `joinLayerId` + `fields` |
| "Nearest X to each Y" | `near` | point `layerId` + point `nearLayerId`; optional `maxDistanceM` |
| "Combine these two layers" | `merge` | `layerId` + `mergeLayerId` (same geometry type) |
| "Merge regions by attribute" | `dissolve` | polygon `layerId`; optional `dissolveField`, `sumFields` |
| "Is X related to Y across regions?" | `correlate_layers` | two region layers |
| "Show X and Y together on one map" | `add_fusion_layer` | two region layers (bivariate) |

Build the input layers first with `build_map` / `add_layer`, then call
`get_state` (free) to get the exact layer ids.

## Read the result

The reply carries a `memo` and `stats` computed by the server, a
`resultLayerId`, and a `contextAnswer`. Use only those numbers in your answer
(see `grounded-map-answers`).

Know what each method does and doesn't claim:

- `hotspot` classes are 99 / 95 / 90 % significance, FDR-corrected. "No
  significant clustering" is a valid finding.
- `zonal` keeps **zero** and **no data** as different classes. Don't call an
  empty-data zone "zero".
- `enrich` leaves unmatched features null — never zero. US only.
- `near` distances are great-circle metres, **not** road or walking distance.
  Say so when the user asked about travel.
- The result replaces its input layer(s); `undo` brings them back.

## Options

- `annotate: true` places one callout on the headline feature (hottest
  cluster, top zone), labelled only with computed values.
- `story: true` makes the shared link open as a short guided read.

Use them when the user is going to share the map.

## Cost

One credit when the result lands. Typed failures — unknown layer, too few
features, more than 10,000 features — are refunded. If a layer is too large,
narrow it first with `filter_layer` or `focus_area`.
