---
name: grounded-map-answers
description: Write the text that accompanies a ShowMeOnMap map using only numbers and facts the tools returned, with sources cited and gaps stated. Use whenever summarising, captioning or answering questions about a map built with ShowMeOnMap.
---

# Grounded map answers

The map is cited and every value on it came from a real source. The text
around it must meet the same bar, or the answer is only as trustworthy as its
weakest sentence.

## The rule

**Every number, name and claim in your answer must come from a tool result in
this conversation.** Not from memory, not from the user's question, not from
what "is usually true".

Grounded sources, in order of preference:

1. `contextAnswer` and analysis `memo` / `stats` — computed by the server.
2. `query_features` results (free) — ask for the top features, counts, ranges.
3. `structured.mapPlan` — layer titles, legend classes, source attributions.

If you need a number you don't have, call `query_features` for it. If no tool
can supply it, say it isn't available.

## Three different statements — never merge them

| Situation | Say |
|---|---|
| The source returned zero features in range | "No M5+ earthquakes were recorded near Japan in the last 30 days (USGS)." |
| The source doesn't cover this place or period | "USGS data here doesn't cover that period — this map can't say." |
| A value is shown as unavailable | "Data for Eritrea is unavailable in the World Bank series." |

"Nothing found" is a finding about the world. "Not covered" is a limit of the
data. Reporting a coverage gap as "nothing found" invents an all-clear.

## Cite

Name the source for each figure in plain words — "(USGS)", "(World Bank,
2023)", "(US Census ACS 5-year)". The sources are in the map's attribution.

## Shape of a good answer

1. One sentence that answers the question, with its number and source.
2. Two or three supporting facts (extremes, counts, a pattern the map shows).
3. What the data cannot tell — only if it matters to the question.
4. The `shareable_url`.

Don't paste JSON, layer specs or tool arguments into the answer.

## Analysis results

A spatial analysis that finds "no significant clustering" is a result, not a
failure — report it as such. Present significance levels exactly as the memo
gives them; don't upgrade "90% confidence" to "clearly".
