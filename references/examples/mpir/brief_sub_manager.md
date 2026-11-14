# Brief: Atlas: Sweep Compute Survey

Assignment from **Atlas** (`session-<uuid>`), under `HUMAN`'s instructions.
You are its Report, titled **Atlas: Sweep Compute Survey**. Supersedes: none.
Send updates only to Atlas, mode `steer` (never `queue`), as
`<WORKSPACE>/_support/common_rules.md` describes. A direct message from
`HUMAN` wins over this brief.

## Task

Find how many GPU-hours each of the last five Atlas sweeps used, and what
that implies for a 72-point sweep.

## Questions

1. **What did each sweep use?** Points, shards, GPUs, wall time, GPU-hours.
2. **What grew faster than the point count?** Queueing, reruns, stragglers.
3. **What would 72 points take?** Give 2–3 candidate layouts, each with its
   expected bottleneck and a GPU-hour range with its basis.

## How you work: MPIR

- **You are the Manager,** on Fable `minimal`: plan, delegate, integrate.
  Keep your own context short; don't do the bulk reading yourself.
- **Planner and reviewers** are your Reports on Fable `xhigh`: one plans
  the survey before implementers start, one reviews the findings before
  you report.
- **Implementers** gather the data, one disjoint slice each (job
  inventory, results tables, launch configs), on free or self-hosted
  models at max effort.
- **Own Reports:** up to 5, titled `Atlas: Sweep Compute Survey: <Task>`. Only
  you create them. They update only you and get no authority beyond this
  brief. Archive each as soon as it reports back.

## Rules

- **Read-only** everywhere. **No contact** with anyone: draft questions
  for `HUMAN` instead.
- **Outputs:** `<WORKSPACE>/sweep_compute_survey.md`; evidence in
  `_support/sweep_compute_survey/`. Label every number M, D or A.
- **Done when:** the write-up exists, its headline numbers are reviewed,
  and you have sent Atlas an update opening with your thread-sheet row.
