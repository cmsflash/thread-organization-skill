# Manager check c01: rows per shard at point 7

Run by Atlas: Sweep Analysis (`session-<uuid>`), <time PDT>, before opening
the Implement stage. In a real round this is a query file and its raw
output, `_support/manager_checks/c01_point7_shard_rows.sql` and `.out`.

**Claim checked:** `plan.md` §3.1: "one row per shard, so a plain average
gives the point's score".

**Query, as run:**

```sql
SELECT shard, count() AS n_rows, round(avg(score), 3) AS score
FROM points
WHERE sweep = 'atlas-eval-base' AND point = 7
GROUP BY shard ORDER BY shard
```

**Raw result (abridged):**

```text
shard  n_rows  score
1      1       0.391
...
9      2       0.447
12     2       0.431
16 rows over 12 shards
```

**Verdict:** corrected. Shards 9–12 have two rows each, so the plain
average is 0.412 [M]; one row per shard gives 0.398 [M]. Fix recorded as
A1 in `plan/amendments.md` and sent with every later brief.
