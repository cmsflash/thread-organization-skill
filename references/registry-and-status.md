# Registry and thread sheet

## Registry file

One file per Manager: `WORKSPACE/Registry.md` for the top Manager,
`WORKSPACE/_support/<area>/Registry.md` for a sub-Manager. Header first:

```markdown
# Atlas: Reports
Last updated: Wed Sep 30 6:47 PM PDT (verified against the Host)
Manager: `Atlas` · session-<uuid> · Host http://127.0.0.1:<port>
Reports to: Dana (human)
```

Then one row per direct Report. Keep a sub-Manager's Reports, threads the
human took over, and archived threads in their own sections, not mixed into
the live table.

| Report | session | model · effort | scope (owns / may write) | task | state | next-action owner | last verified |
|---|---|---|---|---|---|---|---|
| Atlas: Eval Sweep | session-<uuid> | Opus · high | eval sweep; `.worktrees/eval-sweep`; own cluster jobs `atlas-eval-*`; sub-Manager | 36-point sweep | job working, 14/36 points | same thread | 6:40 PM PDT |
| Atlas: Loader Fix | session-<uuid> | Sonnet · high | `core/lib/loader/**` on branch `fix/loader` | padding fix + unit regression | delivered; Atlas reviewing | Atlas | 6:10 PM PDT |
| Atlas: Spec | session-<uuid> | Flash · high | Notion spec "<title>" only | update rung 3 in place | blocked: Host rejected model | Dana (choose fallback) | 5:55 PM PDT |

```markdown
## Managed by Atlas: Eval Sweep (not by Atlas)

| Report | session | state (from Atlas: Eval Sweep's update, 6:38 PM PDT) |
|---|---|---|
| Atlas: Eval Sweep: Shard Rerun | session-<uuid> | rerunning shards 9–12 |

## Reporting directly to Dana (not managed by Atlas)

## Archived
```

Rules:
- `session` is the full ID. Titles are for humans.
- `state` uses the vocabulary in SKILL.md; never "pending" alone.
- `last verified` is when you last read the runtime, not when you last wrote.
- A sub-Manager's section is read-only: ask that Manager, never its Reports;
  when it is urgent, tell `HUMAN` as well.

## Thread sheet

`WORKSPACE/Threads.md` is the canonical human view. It is rewritten in
place, not appended to. Layout:

```markdown
# Atlas: threads

<one line: what evidence this view reflects>

🟢 On track · 🟡 Investigating a problem · 🔵 Waiting on <HUMAN> · 🔴 Off track

## Thread table

For a question, **Status/answer** gives the answer; for a task/query, it
gives progress. **Next action** names the action and its owner.

| Status | Thread | Query | Status/answer | Next action |
|---|---|---|---|---|
| ... |

## 1. <detail section title>
...

## Decisions for <HUMAN>
...
```

Example rows:

| Status | Thread | Query | Status/answer | Next action |
|---|---|---|---|---|
| 🟢 | **Atlas: Eval Sweep** | Run the 36-point sweep | 14/36 points landed; its Report Shard Rerun is rerunning shards 9–12. No detail section. | Same thread: collect at 36/36. |
| 🟡 | **Atlas: Loader Fix** | Fix padding and add regression | Claims a red→green regression; review pending. Details: [§1](#1-loader-fix). | Atlas: re-run tests, read diff. |
| 🔵 | **Atlas: Spec** | Update rung 3 | Blocked before work: Host rejected the model. Details: [§2](#2-spec-model-rejected). | Dana: choose a fallback model. |
| 🟢 | **Atlas: Budget Check** | Is the sweep within budget? | Yes: 31 of 40 GPU-hours used. No detail section. | **Atlas: Eval Sweep**: send final GPU-hours at 36/36. |

Rules:
- **Query → answer or progress.** A question gets its answer in the cell; a
  task gets its progress. Do not answer a question with "in progress".
- **Brief cells.** One or two short sentences. Anything longer goes in a
  numbered section below, linked as `Details: §n`; otherwise write
  `No detail section`.
- **Every Next action starts with its owner**: `Same thread:`,
  `<MANAGER>:`, `<peer Report>:` or `<HUMAN>:`. Bold an owner whose title
  contains `: `, so the owner's title stays separate from the action.
- **Nested Reports get no rows.** A sub-Manager's row summarises its own
  Reports.
- Detail sections are structured explanations, not history; superseded
  detail moves to `_support/archive/`.
- `REGISTRY` holds exact session IDs and scopes; the sheet is for reading.

## Expanded detail (below the table)

One numbered section per row that links to it:

```markdown
## 1. Loader fix
- Claim: witness ids padded with -1; regression `loader_pad_test` red at
  a1b2c3d, green at d4e5f6a.
- Manager check: re-run the test remotely; read the 3-file diff.
- Provenance: branch `fix/loader` @ d4e5f6a; brief in
  `_support/dispatches/20260930_round.json`.
- Next: on pass, ask <HUMAN> for the smoke launch (one job, ~40–60 min on
  4 GPUs, from the last two smokes' 38 and 55 min; breaks if the queue is
  full).
```

## Estimate format

`<range> (<basis>; breaks if <assumption>)`, e.g. "2.5–4 h (22 points left
at 7–11 min each over the last 10 points; breaks if a 3000 s straggler
recurs)". No basis, no number.
