# Brief: Atlas: Sweep Analysis: Plan

Assignment from **Atlas: Sweep Analysis** (`session-<uuid>`), under
`HUMAN`'s instructions. You are its Report, titled **Atlas: Sweep Analysis:
Plan**. Supersedes: none.

**Model:** Fable, xhigh. Keep each reasoning step short.

**Read first:** `<WORKSPACE>/_support/context.md`: the question,
definitions, access, rules and reporting. It is part of this brief.

## Task

Plan the comparison of the two sweeps so that three cheaper implementers
can run it in parallel without overlapping, and two reviewers can check it.
**You plan; you don't do the bulk measurement.** Reconnoitre enough to make
the plan correct: fix the inventory of points, shards and repeat runs, and
validate the before/after join on one point, with row counts.

## Deliverable: `_support/plan/plan.md`

1. **Definitions and formulas** every slice uses.
2. **Inventory tables** for both sweeps, every value labelled M, D or A.
3. **Validated example queries**, as run, with row counts.
4. **Exactly three implementer slices**, each with a disjoint write scope
   `_support/slice_<x>/`. For each: ordered steps, starter queries, output
   files with their columns, stop criteria, a time cap, and what to do when
   a step returns nothing. Write each for a cheaper model to follow
   literally.
5. **Review checklists** for two reviewers: the claims each re-derives.
6. **Risks**, and open questions for `HUMAN`.

## Terms

- **Done when:** `plan.md` has all six parts, the join is validated, and
  you have sent Atlas: Sweep Analysis an update.
- **Authorised:** the context file's read-only access; write only under
  `_support/plan/`.
- **Not authorised:** creating threads (Atlas: Sweep Analysis creates the
  implementers and reviewers), contacting anyone, any cluster write.
- **Own Reports:** none. **Time cap:** ~60 minutes of agent time; at the
  cap, send what you have.
