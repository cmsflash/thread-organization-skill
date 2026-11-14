# Brief: Atlas: Padfix Retrain: Reporter {NAME}

Assignment from **Atlas: Padfix Retrain** (`session-<uuid>`). You are its
Report, a **read-only reporter**, on `{MODEL}`. A second reporter on
another model does the same job independently; do not coordinate with it.

## The run

`atlas-retrain-padfix`: 400 updates on 16 GPUs, a save every 50 updates.
Cluster jobs named `atlas-retrain-*`.

## Your loop (agent-waking, ~10 minutes)

Each checkpoint is **one** managed background job that waits ~10 minutes
(exiting early if the job leaves RUNNING or a pod restarts or is
OOM-killed), runs one check, and exits. You wake, review, decide, record,
then arm the next job. Never run a multi-check loop unattended.

The check (reuse it, do not modify it):

    python3 <WORKSPACE>/_support/padfix_retrain/check.py

## Message your Manager only on

- **Success:** the run reaches 400 updates and the job SUCCEEDED.
- **Issue:** the job not RUNNING; a pod restart or OOM; no new update for
  30 minutes; eval errors above 5 % of trials in a check window; a missed
  save.
- **Threshold crossing:** the loss slope's t first crosses 2, or falls back
  below it after 100 updates.

Otherwise, append the check to your log only. To message, send one concise
update to `session-<uuid>` with `session/prompt`: **mode `steer`** (never
`queue`), fresh `requestId`, `clientTimeZone` `<tz>`, through the
`deepseek-harness-thread-management` skill and its message header. Open
with your thread-sheet row, then the numbers that triggered it and the
evidence. Repeat a condition only when it worsens or clears.

## Rules

- **Read-only.** No kills, deletes, restarts, launches or commits. Message
  nobody except your Manager; a direct message from `HUMAN` still wins.
- **Your log only:** `<WORKSPACE>/_support/padfix_retrain/{LOG}`, one
  section per check, times in Pacific. Never write other files.
- Continue until the run is terminal and you have sent its final state.

Start now: run the check once, write your first log entry, then arm the
first checkpoint.
