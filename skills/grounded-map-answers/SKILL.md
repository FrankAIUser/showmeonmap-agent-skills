---
name: grounded-map-answers
description: Write the text that accompanies a ShowMeOnMap map using only numbers and facts the tools returned, with sources cited and gaps stated. Use whenever summarising, captioning or answering questions about a map built with ShowMeOnMap.
---

# Grounded map answers

The map is cited and every value on it came from a real source. The text
around it must meet the same bar, or the answer is only as trustworthy as its
weakest sentence.

## The rule

**Every statement about the data — numbers, feature names, statuses, places
of features — must come from a tool result in this conversation.** Not from
memory, not from the user's question, not from what "is usually true".

This governs claims about the data. A clarifying question may name likely
options from general knowledge ("Springfield, Illinois or Missouri?").

Grounded sources, in order of preference:

1. Analysis `memo` / `stats` and `contextAnswer` — computed by the server.
2. `structured.mapPlan.insights` (headline + evidence) and layer `subtitle`s.
3. `query_features` results (free) — top features, counts, ranges, attributes.
4. Legend attribution — the source names.

If you need a number you don't have, call `query_features` for it. If no tool
can supply it, say it isn't available.

## Derived numbers

You may compute simple values from tool data — a distance from two returned
coordinates, a difference, a share. Label them as computed and approximate
("about 70 km away in a straight line"). Never present your own arithmetic as
a server analysis result.

## Places and labels

Describe where features are using names the data carries (a feature's place
field, an incident name). If features carry no names, give coordinates or
direction from a named feature — don't attach region names from memory to
unlabelled cells; you may be placing them in the wrong state or offshore.

Quote the source's own status vocabulary ("Under Control", "Being Held") rather
than translating it into "contained" or "active".

## Three different statements — never merge them

| Situation | Say |
|---|---|
| The source returned zero features in range | "No M5+ earthquakes were recorded near Japan this month (USGS)." |
| The source doesn't cover this place or period | "USGS data here doesn't cover that period — this map can't say." |
| A value is shown as unavailable | "Data for Eritrea is unavailable in the World Bank series." |

"Nothing found" is a finding about the world. "Not covered" is a limit of the
data. Reporting a coverage gap as "nothing found" invents an all-clear. When
the envelope says nothing about coverage, check it: features present around the
empty area mean the source covers it.

## Cite

Name the source for each figure in plain words — "(USGS)", "(World Bank,
2023)", "(US Census ACS 5-year)". The sources are in the map's attribution.

## Shape of a good answer

1. One sentence that answers the question, with its number and source.
2. Two or three supporting facts (extremes, counts, a pattern the map shows).
3. What the server applied that the user didn't ask for (a default threshold,
   a wider area) and what the data cannot tell — only where it matters.
4. The `shareable_url`.

Don't paste JSON, layer specs or tool arguments into the answer.

## Analysis results

A spatial analysis that finds "no significant clustering" is a result, not a
failure — report it as such. Present significance levels exactly as the memo
gives them; don't upgrade "90% confidence" to "clearly".
