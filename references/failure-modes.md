# Failure modes and their checks

| failure | check |
|---|---|
| Subagent spawned when the human asked to dispatch an agent | A Report is a top-level thread made with `session/create` |
| Manager instructs another Manager's Report | Manage direct Reports only; go through their Manager |
| Update skips the Report's own Manager | Updates go to the direct Manager only |
| Peer told to send its dispatcher a summary | A Peer reports only to the human; its brief asks for nothing back |
| Relayed rule followed over the human's direct message | The human's message wins; act, then tell the Manager |
| Title hides who manages a Report | One prefix level per Manager |
| Authority widened down the chain | A brief grants no more than its Manager holds |
| Manager archived with open Reports | Move each Report to another Manager or the human first |
| Stale queued brief runs after a newer one | Inspect inbox before sending; briefs name what they supersede |
| Unattended loop logs checks nobody acts on | Monitoring: one background job per checkpoint, and the thread decides at each |
| Monitor wakes only when the job ends | The waiter also exits on its interval, so the thread sees every checkpoint |
| Ordinary waits run as tight monitoring loops | The checkpoint cadence is only for training runs and explicitly requested monitoring |
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
| Finished Report left open in the thread list | Archive a direct Report in the turn its DONE update arrives and its deliverable exists, without waiting for review |
| Untracked configs lost on cleanup | Copy to `_support/archive/<topic>/` before archive or removal |
| Shared worktree changed mid-launch | One worktree per building thread |
| Polling burns a long history | Background waiters that notify |
| Approval re-asked or silently widened | Execute the approved set; new targets need new approval |
| Owned PR merged, or a human reviewer's thread resolved, unasked | Owning a PR authorises neither; each needs approval of that exact action |
| Question answered with "in progress" | Sheet rule: a question gets its answer; a task gets its progress |
| Next action with no owner | Every Next action starts with its owner |
| Secret pasted into an update | Never print tokens, cookies, DSNs |
