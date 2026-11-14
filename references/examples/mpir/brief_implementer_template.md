# Brief: Atlas: Sweep Analysis: {TITLE}

Assignment from **Atlas: Sweep Analysis** (`session-<uuid>`), under
`HUMAN`'s instructions. You are its Report, titled **Atlas: Sweep Analysis:
{TITLE}**. Supersedes: none.

**Model:** `{MODEL}` (free or self-hosted), max effort. You are one of
three implementers working in parallel on disjoint slices.

## Read first, in order

1. `<WORKSPACE>/_support/context.md`. It is part of this brief.
2. `<WORKSPACE>/_support/plan/plan.md`. **Your slice is "{SLICE}".** Follow
   its steps literally and in order. Don't do other slices' work or write
   their files.
3. `<WORKSPACE>/_support/plan/amendments.md`. **Where it differs from
   `plan.md`, it wins.**

{EXTRA}

## Task

Execute slice {SLICE} of the plan and produce its output files.

## How to work

- **Save as you go.** Write every query to `{DIR}/queries/` as run, with
  its raw result next to it. A reviewer re-runs them.
- **When a step returns nothing**, check its pieces separately and record
  what you found. Don't guess a value or extrapolate from other points;
  leave the cell empty and say why.
- **Label every number** M, D or A.
- **If the plan is wrong**, record the correction, follow the evidence, and
  say so in your update.

## Terms

- **Done when:** the slice's stop criteria are met, its output files exist
  under `{DIR}/`, and you have sent Atlas: Sweep Analysis an update listing
  the files, headline numbers, gaps and corrections to the plan.
- **Authorised:** the context file's read-only access.
- **Not authorised:** any cluster write; contacting anyone; creating
  threads; writing outside `{DIR}/`. **Own Reports:** none.
- **Blockers:** send the exact error at once. Don't retry silently.
- **Time cap:** {CAP}. At the cap, send what you have and what remains.
