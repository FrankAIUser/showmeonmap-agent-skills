---
name: recurring-map-workflows
description: Save a ShowMeOnMap map recipe as a parameterised workflow and re-run it on fresh data, alone or for many places at once. Use when the user wants the same map again later ("every Monday", "refresh this"), or the same map for a list of places ("for each of these 12 districts").
---

# Recurring map workflows

A workflow is an ordered list of plain-language steps: step 1 builds a map,
later steps change it. Re-running it re-executes the steps on **fresh data** —
it is not a snapshot.

## Save

Turn the conversation that produced a good map into steps. Replace anything
that should vary with a `{slot}` and declare it in `params`.

```json
{
  "name": "Weekly quake check",
  "steps": [
    { "instruction": "earthquakes above magnitude {min_mag} near {place} in the last 7 days" },
    { "instruction": "only show the ones deeper than 10 km" }
  ],
  "params": [
    { "name": "place", "label": "Place", "kind": "place" },
    { "name": "min_mag", "label": "Minimum magnitude", "kind": "number", "default": "4.5" }
  ]
}
```

- Up to 12 steps and 8 parameters. Parameter `kind` is `place`, `number`,
  `text` or `timerange`.
- Write steps the way the user would type them. Each step is billed like a
  typed request.
- Put the place, time window and threshold into step 1 — the same rule as
  `build-and-refine-a-map`.
- Every `{slot}` used in a step must be declared.

`save_workflow` returns `workflow_id` and `estimated_credits` per run. Tell
the user the estimate.

## Run

`list_workflows` (free) shows saved workflows, their parameters and per-run
estimates. `run_workflow` executes one:

- **Single run:** `params: { "place": "Lima", "min_mag": "4.5" }`.
- **Batch:** sweep **one** parameter over 2–20 values —
  `batch: { "param": "place", "values": ["Lima", "Quito", "Bogotá"] }`.
  Each value is a full, separately billed run (steps × values credits).

**Confirm the total with the user before a batch run**: "3 steps × 12
districts ≈ 36 credits — go ahead?"

## Results

| `status` | Meaning | Do |
|---|---|---|
| `done` | Map built | Share the `shareable_url`; summarise per `grounded-map-answers` |
| `partial` | Some batch values failed or paused | Report which, per value; the rest are valid |
| `paused` | A step needs a decision | Ask the `question`, then run again with the answer |
| `failed` | Stopped at a failing step | Say which step failed and why |

Escalations inside a run can cost more than the estimate; say so when quoting.
