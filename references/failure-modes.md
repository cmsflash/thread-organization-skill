# Failure modes and their checks

| failure | check |
|---|---|
| Stale queued brief runs after a newer one | Inspect inbox before sending; briefs name what they supersede |
| Turns continue after the human pauses | On pause: kill waiters, no re-arm, no dispatch, no "check-in" turns |
| Model switch fails on billing or availability | Confirm the next turn ran on the model; record infra, not quality |
| Model assumed available from the catalog | Verify reachability before planning on it |
| History made unloadable by screenshots | No screenshots in long threads; numeric verification |
| Long-context `max` turns time out | Default `high`; short context for `max` |
| Arbitrary ETA | Range with basis and breaking assumption, or "no estimate" |
| Agent turn ended read as job ended | Separate thread and job states; check the scheduler |
| RUNNING read as training | Require advancing steps or trials |
| Directory existing read as a save | Require the tracker or manifest |
| Goal closed while dependent work runs | Close only when the objective holds and no owned job is live |
| Watcher hangs on failure | Every terminal state is terminal |
| Diagnosis from a truncated log | Pull the full log or the exact traceback |
| Trend called from two points | Pre-set rule; noise floor; uncertainty |
| Relayed claim later wrong | Re-derive before relaying; retract explicitly |
| Registry drift and duplicate rows | One row per thread; "last updated" on every edit |
| Untracked configs lost on cleanup | Copy to `_support/archive/<topic>/` before archive or removal |
| Shared worktree changed mid-launch | One worktree per building thread |
| Polling burns a long history | Background waiters that notify |
| Approval re-asked or silently widened | Execute the approved set; new targets need new approval |
| Question answered with "in progress" | Sheet rule: a question gets its answer; a task gets its progress |
| Next action with no owner | Every Next action starts with its owner |
| Secret pasted into a report | Never print tokens, cookies, DSNs |
