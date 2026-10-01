# Registry and thread sheet

## Registry file

One file in `WORKSPACE`, e.g. `REGISTRY.md`. Header first:

```markdown
# Atlas — managed threads
Last updated: Wed Sep 30 6:47 PM PDT (verified against the Host)
Driver: `Atlas` · session-<uuid> · Host http://127.0.0.1:<port>
Human: <name>
```

Then one row per thread. Keep archived threads in a separate section, not
mixed into the live table.

| thread | session | model · effort | scope (owns / may write) | task | state | next-action owner | last verified |
|---|---|---|---|---|---|---|---|
| Atlas: Eval sweep | session-<uuid> | Opus · high | eval driver; `.worktrees/eval-sweep`; own cluster jobs `atlas-eval-*` | 36-point sweep | job working, 14/36 points | same thread | 6:40 PM PDT |
| Atlas: Loader fix | session-<uuid> | Sonnet · high | `core/lib/loader/**` on branch `fix/loader` | padding fix + unit regression | reported; under review | Atlas thread | 6:10 PM PDT |
| Atlas: Spec | session-<uuid> | Flash · high | Notion spec "<title>" only | update rung 3 in place | blocked: Host rejected model | Dana (choose fallback) | 5:55 PM PDT |

Rules:
- `session` is the full ID. Titles are for humans.
- `state` uses the vocabulary in SKILL.md; never "pending" alone.
- `last verified` is when you last read the runtime, not when you last wrote.

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
| 🟢 | **Atlas: Eval sweep** | Run the 36-point sweep | 14/36 points landed. No detail section. | Same thread: collect at 36/36. |
| 🟡 | **Atlas: Loader fix** | Fix padding and add regression | Thread reports red→green regression; review pending. Details: [§1](#1-loader-fix). | Atlas thread: re-run tests, read diff. |
| 🔵 | **Atlas: Spec** | Update rung 3 | Blocked before work: Host rejected the model. Details: [§2](#2-spec-model-rejected). | Dana: choose a fallback model. |
| 🟢 | **Atlas: Budget check** | Is the sweep within budget? | Yes: 31 of 40 GPU-hours used. No detail section. | Atlas: Eval sweep: report final GPU-hours at 36/36. |

Rules:
- **Query → answer or progress.** A question gets its answer in the cell; a
  task gets its progress. Do not answer a question with "in progress".
- **Brief cells.** One or two short sentences. Anything longer goes in a
  numbered section below, linked as `Details: §n`; otherwise write
  `No detail section`.
- **Every Next action starts with its owner**: `Same thread:`,
  `<DRIVER> thread:`, `<peer thread>:` or `<HUMAN>:`.
- Detail sections are structured explanations, not history; superseded
  detail moves to `_support/archive/`.
- `REGISTRY` holds exact session IDs and scopes; the sheet is for reading.

## Expanded detail (below the table)

One numbered section per row that links to it:

```markdown
## 1. Loader fix
- Claim: witness ids padded with -1; regression `loader_pad_test` red at
  a1b2c3d, green at d4e5f6a.
- Driver check: re-run the test remotely; read the 3-file diff.
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
