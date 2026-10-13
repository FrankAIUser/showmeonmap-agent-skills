---
name: compare-regions
description: Compare a statistic across countries, states or regions with ShowMeOnMap (World Bank, Eurostat, US Census), including two-variable maps and correlation. Use for "X by country/state", "across Europe", "per capita", "X vs Y".
---

# Compare regions

> DRAFT — outline only.

- Build: one `build_map` naming indicator + geography + year if given ("GDP per capita across Europe, 2023").
- Answer: highest / lowest via `query_features`; year and source for every value; regions shown as unavailable are named as unavailable, never estimated.
- Percent indicators are already percentages — don't multiply.
- Two variables: `add_fusion_layer` for a bivariate map; `correlate_layers` for the coefficient. Correlation ≠ cause — say so once.
- Trap: countries a source reports under a different name or merged geography — report what the source says, don't reconcile from memory.
