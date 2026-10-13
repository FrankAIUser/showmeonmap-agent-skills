---
name: neighbourhood-profile
description: Profile what is around a place with ShowMeOnMap — schools, hospitals, shops, transit stops from OpenStreetMap — and, in the US, who lives there via Census enrichment. Use for "what's near", "around", "within walking distance", "who lives near".
---

# Neighbourhood profile

> DRAFT — outline only.

- Ask which place first if the name is shared by several cities.
- Build the POI layer with place in the query; narrow with `focus_area` (place / street corridor / intersection).
- Demographics (US only): `run_analysis` `enrich` with an ACS indicator; unmatched stays null.
- "Nearest X": `run_analysis` `near` — straight-line metres, not walking time; say so.
- OpenStreetMap completeness varies; an absent shop is "not mapped in OpenStreetMap", not "doesn't exist".
