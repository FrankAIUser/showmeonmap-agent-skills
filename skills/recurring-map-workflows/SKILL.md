---
name: recurring-map-workflows
description: Save a ShowMeOnMap map recipe as a parameterised workflow and re-run it on fresh data, alone or for many places at once. Use when the user wants the same map again later ("every Monday", "refresh this"), or the same map for a list of places ("for each of these 12 districts").
---

# Recurring map workflows

A workflow is an ordered list of plain-language steps: step 1 builds a map,
later steps change it. Re-running it re-executes the steps on **fresh data** —
it is not a snapshot.

**Workflows run on demand.** There is no scheduler: "every Monday" means the
user (or their agent) runs it each Monday. Say so; don't promise automatic runs.

## Save

Turn what the user asked for into steps — nothing more. Every extra step is an
extra credit on every run. Replace anything that should vary with a `{slot}`
and declare it in `params`.

```json
{
  "name": "Weekly quake check",
  "steps": [
    { "instruction": "earthquakes above magnitude {min_mag} near {place} in the last 7 days" }
  ],
  "params": [
    { "name": "place", "label": "Place", "kind": "place" },
    { "name": "min_mag", "label": "Minimum magnitude", "kind": "number", "default": "4.5" }
  ]
}
```

- Up to 12 steps and 8 parameters. `kind` is `place`, `number`, `text` or
  `timerange`; `default` is a string.
- Write steps the way the user would type them. Each step is billed like a
  typed request. Put place, time window and threshold into step 1.
- Every `{slot}` used in a step must be declared.
- `list_workflows` (free) first if the user may already have a similar one.

`save_workflow` returns `workflow_id` and `estimated_credits` per run. Tell
the user the estimate.

## Run

`run_workflow` executes one:

- **Single run:** `params: { "place": "Lima" }` — omitted params use their
  defaults.
- **Batch:** sweep **one** parameter over 2–20 values —
  `batch: { "param": "place", "values": ["Lima", "Quito", "Bogotá"] }`.
  The swept parameter must not also appear in `params`. Each value is a full,
  separately billed run.

**Quote and confirm before any batch**: total ≈ `estimated_credits` × number
of values ("1 credit × 3 cities ≈ 3 credits — go ahead?"). Escalations inside
a run can cost more; say the figure is an estimate. If a batch value is an
ambiguous place, state the meaning you'll use (or ask) before running.

## Results

| `status` | Meaning | Do |
|---|---|---|
| `done` | Map built | Share the `shareable_url`; summarise per `grounded-map-answers` |
| `partial` | Some batch values failed or paused | Report which, per value; the rest are valid |
| `paused` | A step needs a decision | Ask the `question`, then run again with the answer |
| `failed` | Stopped at a failing step | Say which step failed and why |
