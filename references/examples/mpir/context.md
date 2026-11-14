# Atlas sweep comparison: common context for every Report

Agent-drafted by Atlas: Sweep Analysis (`session-<uuid>`) for `HUMAN`,
<time PDT>.

## The question

`HUMAN`'s ask: did the loader padding fix change the 36-point eval sweep,
and by more than rerun noise? Answer per sweep point and overall.

## What "changed" means

- **delta:** a point's score after the fix minus its score before it;
- **noise floor:** the spread of its score across repeat runs of one code;
- **changed:** |delta| above twice the noise floor. Give per-point values.

## The two populations

**Before:** sweep `atlas-eval-base`. **After:** sweep `atlas-eval-padfix`,
the same 36 points. The planner fixes the exact inventory.

## Known facts from earlier work (verify before relying on them)

- Each point writes one row per shard to the `points` table; 12 shards.
- Shards 9–12 of `atlas-eval-base` were rerun, so those rows exist twice.
- Job logs are kept for a rolling 14 days. Save aggregates as you go.

## Access (all read-only)

- **Results:** `python3 tools/q.py "<SQL>"` prints JSON.
- **Cluster:** get, describe and logs on jobs named `atlas-eval-*`.

## Rules

1. Read-only everywhere. No contact with anyone: put questions for `HUMAN`
   in your output.
2. Label every number **[M]** measured, **[D]** derived or **[A]** assumed.
3. Write only inside your own directory under `_support/`. Times in Pacific.

## Reporting

Send updates only to **Atlas: Sweep Analysis** (`session-<uuid>`) on the
Host `http://127.0.0.1:<port>`: fresh `requestId`, **mode `steer`** (never
`queue`), `clientTimeZone` `<tz>`, through the
`deepseek-harness-thread-management` skill and its message header. Open
with your row: `Status | Thread | Query | Status/answer | Next action`.
Send one when you finish, and at once on a blocker, with the exact error.
Message no other thread. A direct message from `HUMAN` wins over your
brief: act on it, then tell Atlas: Sweep Analysis what you did.
