# Plan amendments (override `plan.md` where they differ)

Agent-drafted by Atlas: Sweep Analysis (`session-<uuid>`) after reviewing
`plan.md`, <time PDT>. Evidence is in `../manager_checks/`.

## A1. Per-point score: drop rerun duplicates first (replaces §3.1's SQL)

**Problem.** §3.1 averages every row of a point. Shards 9–12 of
`atlas-eval-base` were rerun, so those shards count twice: point 7 comes
out at 0.412 against 0.398 from one row per shard [M:
`manager_checks/c01_point7_shard_rows.md`].

**Fix.** Keep the latest row per point and shard, then average. Validated
on point 7: 16 rows in, 12 shards out [M: same check].

```sql
SELECT point, avg(score) AS score, count() AS shards
FROM (
  SELECT point, shard, argMax(score, finished_at) AS score
  FROM points
  WHERE sweep = '<sweep>'
  GROUP BY point, shard)
GROUP BY point ORDER BY point
```

Use this for every per-point score (slices A and B, and the reviewers).

## A2. Decisions on the plan's open questions (§6)

These are the Manager's defaults; `HUMAN` may override any of them.

1. **Points with fewer than 12 shards:** report them, marked incomplete;
   never pool them with complete points.
2. **Noise floor:** from the three repeat runs of `atlas-eval-base` only.

## A3. Clarifications

- Report the before and after scores side by side, with the shard count.
- Slice A's stop check stays: reproduce point 7 within 0.002 of 0.398.
