---
name: thread-organization
description: "Lets a thread use other top-level DeepSeek Harness threads as a more powerful substitute for subagents. A Manager thread delegates to Reports: full threads the human sees and messages in the UI, which persist, take follow-ups, and can manage Reports of their own (hierarchical management). Use to dispatch an agent, coordinate threads, monitor a training run or a process on request, or run a named pattern such as MPIR. Triggers: 'dispatch an agent to', 'dispatch this to the X thread', 'dispatch a peer', 'keep monitoring', 'use MPIR', 'run status table', 'report tree'."
---

# Thread organization

A **Manager** thread delegates work to **Reports**: other top-level DeepSeek
Harness threads, used in place of subagents. A Report is an ordinary thread
in the project's thread list, so the human can read, message and steer it
and answer its approvals directly. It keeps its own history, model and
worktree, outlives the Manager's turns and even the Manager, takes follow-up
tasks, and can manage Reports of its own. A subagent is tied to its parent:
DSH allows only direct children by default, and a child can message its
parent only while the parent is live.

When the human says "dispatch an agent" or "give this to another thread",
create or reuse a Report, never a subagent: a top-level thread in the same
project, so the work is visible to the human in the UI.

For every Host API call (create, list, read a reply, prompt, rename,
archive, wait), use `deepseek-harness-thread-management`; this skill never
restates transport.

## Terms

- **Manager**: the managing thread. It briefs, reviews, and answers for its
  Reports' results.
- **Report**: a managed thread, with exactly one Manager. The capital keeps
  the noun apart from the verb: a Report *reports to* its Manager, and each
  message it sends is an *update*.
- **Hierarchical management** is allowed: a Report that manages Reports of
  its own is a **sub-Manager**. A Manager's **direct Reports** are the
  threads it manages itself; Reports further down are **nested Reports**.
- **`HUMAN`**: the person who approves. The **top Manager** reports to
  `HUMAN` and is not a Report.
- **Peer**: a top-level thread that reports directly to `HUMAN` and is
  outside the chain of whichever thread dispatched it, so its title has no
  Manager prefix. It is not that thread's Report: it never reports back to
  it (see Dispatch). A Report that `HUMAN` takes over temporarily is still a
  Report: it keeps its prefix and returns to its Manager on handback.
- **Siblings**: Reports of the same Manager.

## Programme parameters

Read these from the programme's workspace before acting; never hard-code them:

| parameter | meaning |
|---|---|
| `MANAGER` | this Manager's exact session ID, title and Host origin |
| `REPORTS_TO` | this Manager's own Manager (exact session), or `HUMAN` for the top Manager |
| `PREFIX` | title prefix for direct Reports: `MANAGER`'s title plus `: ` |
| `WORKSPACE` | the one folder for every programme artifact |
| `SHEET` | the human-facing thread sheet, `WORKSPACE/Threads.md`, kept by the top Manager |
| `REGISTRY` | this Manager's registry of its Reports: `WORKSPACE/Registry.md` for the top Manager, `WORKSPACE/_support/<area>/Registry.md` for a sub-Manager |
| `LADDER` | model-ladder rules and ledger, if the programme uses one |
| `HUMAN` | the person who approves; nobody else's approval counts |

If one is missing, ask once and record the answer in `REGISTRY`.

## Hierarchy

- **Manage direct Reports only.** Instruct, review, rename and archive only
  your direct Reports. Reach a nested Report through its Manager, and tell
  `HUMAN` as well when it is urgent.
- **Update only your own Manager**, never past it. A Report that manages
  others summarises its Reports in its own updates.
- **The human outranks the chain.** A direct message from `HUMAN` wins over
  any relayed rule, such as a freeze or a scope limit. Act on it, then tell
  your Manager what you did.
- **Titles show the chain**, in Title Case, one prefix level per Manager:
  under `Train`, a Report is `Train: Main Migration` and its Report is
  `Train: Main Migration: Review Loop`, never `Train: Review Loop`.
- **Authority only narrows.** A brief grants no more than its Manager holds.
  A Report takes on Reports of its own only when its brief or `HUMAN` allows
  it, and tells its Manager when it does.
- **Only `HUMAN` restructures.** When a Report moves to another Manager, the
  old one stops instructing it and marks its row "transferred to
  <Manager>"; the new one adopts it. A thread `HUMAN` takes over for good
  leaves the chain and becomes a Peer; one taken over temporarily stays a
  Report until handback. List both under "Reporting directly to `HUMAN`",
  and instruct neither. Before a Manager is
  archived, each of its open Reports moves to another Manager or to `HUMAN`.

## Authority

The human's approval is scoped to an exact action, target and content. Do
not widen it, and do not ask again for what was clearly approved.

- **What a thread launched stays its own.** A job or
  resource a thread launched remains under that thread's authority: it may
  patch, scale, restart or delete it, relaunch a replacement within the same
  task, and clean up its leftovers without asking again, then reports what it
  did. Its Manager may direct it to; a thread that takes over its work
  inherits the authority. It never extends to jobs a person or another thread
  launched, or to shared resources.
- **Needs approval of the specific action:** deleting, replacing, relaunching
  or scaling a live job someone else launched, even inside an approved plan;
  archiving any thread other than your own finished direct Report (see Close
  and archive); removing a queued message; answering a thread's pending
  approval or question; any contact with another person (review requests,
  mentions, DMs, comments); commits, pushes and PRs where the repo requires
  confirmation; merging a PR or resolving a human reviewer's thread, even on
  a PR the thread owns. Owning a PR does cover resolving review agents'
  threads, such as Devin's.
- **Approved once, then executed:** when the human approves "archive A, B,
  C", archive A, B, C without re-asking. A fourth target, a different
  resource, or a broader action is a new request.
- **Approvals disabled in the harness is a rejection, not permission.** A
  rejected action stays undone and becomes a blocked row.
- A Report inherits only what its brief grants. It never self-authorises a
  change to what a run measures (data, hyperparameters, limits) after a
  failure; it sends an update and waits. Fixing, relaunching or deleting its
  own run is covered by the first bullet.

## Registry: the durable truth

Keep one row per direct Report in `REGISTRY`: exact full session ID, title,
model and effort, owned scope, write scope, current task, state,
next-action owner, last verified time. A sub-Manager's Reports go in a
separate read-only section, with their state as of that Manager's last
update. Format and example:
[registry-and-status.md](references/registry-and-status.md).

- **Exact sessions only.** Resolve and store the full `session-…` ID. Never
  act on a title or a truncated ID; titles collide and change.
- **Last known is not current.** A registry row says what was true when it
  was verified. Before a decision, or an update you send, that depends on
  runtime state, read the current state (running flag, latest committed
  reply, job status) and label anything not re-read as "last verified
  <Pacific time>".
- Edit the row at every state change, and refresh the file's "last
  updated" line when you do. Remove or strike superseded rows instead of
  adding a second row for the same thread.

## Adopt a thread

When an existing thread comes under management: confirm the exact session,
read its latest reply, rename it to `PREFIX<Task>` (keep the old title in
the registry), record its model, scope, open work, worktree and unpushed
branches, then add it to `REGISTRY`, `SHEET` and the ladder ledger. If it
already manages Reports, it keeps them and retitles them itself. Renaming is
an approved write only when the human asked for adoption.

## Dispatch

Default to delegating. The Manager plans, reviews and relays; Reports do the
work. Brief template and a filled example:
[dispatch-brief.md](references/dispatch-brief.md).

- **A new Report is a top-level thread**: create it with `session/create` in
  the Manager's own DSH project (Host workspace), so it shows in the same
  thread list, and title it `PREFIX<Task>`.
- **A Peer does not report back.** When `HUMAN` asks for
  a Peer, create it in the same project without `PREFIX` and brief it to
  report to `HUMAN` ([Peer brief](references/dispatch-brief.md#peer-brief)).
  The brief must not ask it to send you updates, summaries or results, and
  after sending it you do not instruct, wait on or review the Peer. Record
  its session under "Reporting directly to `HUMAN`" in `REGISTRY`.
- **First reply restates the brief.** A Peer, or a Report
  whose first reply goes to `HUMAN`, opens that first turn's final answer by
  repeating or summarising the questions or tasks it was dispatched with, so
  a thread read cold shows what it is answering. The final answer is the
  message after the turn's last tool call. A restatement written before the
  tool calls does not count: the GUI folds that text into the work steps, so
  the answer `HUMAN` reads starts without it.
- **One owner per area.** Route follow-up work to the Report that already
  owns the area. Create a new Report only when the owner's history would
  dominate the cost, or the work is independent.
- **Disjoint write scopes.** Each brief names the files, worktree and cluster
  resources the Report may change. Two Reports never share a worktree they
  build from; a Report that builds images gets its own
  `<repo>/.worktrees/<name>`.
- **Dispatch independent work together**, in one round, each with its own
  brief. Record the round (briefs, models, prompt request IDs) in
  `WORKSPACE`.
- **Always send with `mode: steer`, never `queue`**, whether it's a brief, an
  approval, a correction or an update to a Manager. A queued
  message waits for the target's whole turn to end, and a monitoring thread's
  turn can last hours: a queued approval sits unread behind a 30-minute
  checkpoint wait. Steer lands at the target's next step boundary;
  on an idle thread it starts a turn like queue does. It still can't interrupt
  a tool call in progress, so a thread blocked in a long wait reads it when
  the wait returns. Keep checkpoint waits bounded so that delay stays short.
- **Before sending to a Report, inspect its pending inbox.** A still-queued
  older brief will run after your new one. Make the new brief say what it
  supersedes and propose removing the stale item; do not just append.
- **Prompt accepted means inbox admission only.** Record the request ID; the
  Report has not started until a running turn is observed.

## Choose the model

Use the programme's ladder when it has one; rules in
[model-ladder.md](references/model-ladder.md). In short: step down one rung
per task while work holds up, change rung only between tasks, keep high-risk
work one rung above the floor, and honour any model the human names.

- **Always set the model yourself; never inherit the Host default.** Every
  thread you create (Report, Peer, any pattern's role) gets an explicit
  `session/selectModel` call right after `session/create`, before its brief.
  The Host default changes without notice, so a new thread can silently get
  a free or self-hosted model or a low effort.
  Default when neither the human nor a ladder says otherwise: Fable
  (`anthropic/claude-fable-5-2`) at `medium` for routine or well-specified
  work, and at `xhigh` for planning, review, design discussion and anything
  complex. Use Fable `max` sparingly, only when `xhigh` falls short. A
  Manager thread that mostly briefs and relays, an MPIR's Manager
  included, runs on Fable `minimal`. Record the choice in `REGISTRY`.
- **Verify availability before relying on a switch.** After changing a
  Report's model, confirm its next turn actually started on that model.
- **A provider or Host failure is not a quality verdict.** Billing errors,
  "model unavailable" and transport timeouts before any work are infra rows:
  record the exact error, do not step the Report up or down for it, and
  follow only the fallback the human authorised.
- **Frontier models sometimes block a request.** A turn that ends `blocked`
  with zero steps, again on a resend, is the model refusing that turn, not
  the Report failing. Ways out, unordered: pick per situation:
  - Cut the blocked turn and reissue a paraphrase.
  - Swap the Report to a similar-level model from the other provider
    (Anthropic ↔ OpenAI).
  - Start a new thread with the same brief, then archive the stuck one.
- **Effort**: `medium` unless the default above, a ladder or the human calls
  for more. Keep an `xhigh` or `max` Report's context short; long-context
  `max` turns hit stream-idle timeouts.

## Wait without polling

- Long waits run as **background waiters** (the RPC skill's turn waiter, or
  a job watcher) that survive across turns; a waiter's completion notice
  starts your next turn. Track every job ID. Before ending a turn, account
  for each one: collect those that settled, leave running those whose notice
  you need, and kill the rest.
- A watcher must treat **every** terminal state as terminal: success,
  failure, cancellation, eviction, deletion. One that only matches success
  hangs forever on a failure.
- No repeated status checks that re-read a long history. A check cadence, if
  any, is long (about hourly) and does real work each time. Monitoring, below,
  is the exception.
- **When the human pauses or stops the programme**, kill waiters, do not
  re-arm them, do not dispatch, and leave queued items alone until told.
  Pausing means no further turns, including "just checking" ones.

## Monitoring

This applies only to training runs, and to processes `HUMAN` explicitly asks
a thread to monitor ("keep monitoring", "monitor it until it finishes"). The
thread that owns the run is its monitor. Other waits follow the previous
section.

- **The thread wakes at every checkpoint and decides.** Each checkpoint is one
  managed background job. It waits out the interval, but wakes early when the
  run ends, fails or restarts (for a cluster job: leaves RUNNING, or a pod
  restarts or is OOM-killed). It then runs one thorough check and exits. The job's completion notice starts the thread's
  turn. Read the check and decide: continue, diagnose, fix, kill, relaunch,
  or ask `HUMAN`. Arm the next checkpoint only after that decision.
- **Not monitoring**: a background loop that runs several checks unattended
  and only logs them, or a waiter that wakes the thread only when the job
  ends. The thread must be active at each checkpoint to make decisions.
- **Cadence**: 5 minutes, then 30 minutes, then hourly, or what `HUMAN` sets.
  Return to a short interval after a relaunch or fix.
- **Each check is thorough**: for a training run, liveness, step progress,
  reward and its health metrics, saves, memory, restarts and evictions.
  Append one line per checkpoint to the run's monitor log in `WORKSPACE`,
  with what was decided.
- **On a crash**, find the root cause. For a run the thread launched, recover
  without asking (see Authority): fix it with a test that reproduces it,
  relaunch under the next free run name, keep monitoring the relaunch, delete
  the run's own leftover resources at once, and report what you did. When the
  cause is unclear, or the fix would change what the run measures, stop and
  send an update with the exact question for `HUMAN`.
- **A Manager monitors through its Reports.** It checks their updates and
  logs on its own slower checkpoints, and does not re-run their checks.

## States: say exactly which one

Never collapse these. Each has its own evidence:

| layer | state | evidence |
|---|---|---|
| task | drafted → approved → submitted | prompt accepted (inbox admission) |
| | running → delivered | running turn observed → `turn/end` with a final reply |
| | reviewed | Manager verdict recorded |
| job | submitted → admitted | resource accepted → pods scheduled and placed |
| | running → working | processes up → steps or trials advancing in its metrics |
| | checkpoint saved | tracker or manifest committed, not a directory existing |
| | terminal | scheduler reports SUCCEEDED / FAILED / deleted |
| experiment | complete | terminal job **and** results checked against pre-set criteria |

A thread's turn ending is not its job ending. A job RUNNING is not training.
A goal is complete only when its objective holds, not when an agent says so.

## Blockers and dependencies

Every open row's next action names exactly one **owner**:

- **Same thread**: the Report itself is working.
- **`MANAGER`**: the Manager owes the next action.
- **`<sibling>`**: another named Report of the same Manager.
- **`HUMAN`**: a decision or approval; state the exact question. A thread
  that reports to `HUMAN` puts it in its decisions.md, as the `decisions-md`
  skill describes.

When the wait is on an external system (scheduler, provider, CI), name the
owner who acts once it clears and the system it is waiting for, e.g. "Same
thread: rerun once CI is green".

An interruption, error or rejection becomes an explicit **blocked** row at
once, with the exact error text and where it was observed. Do not retry
silently, and do not spend more than one short attempt diagnosing before
sending an update. Record dependencies between rows ("starts after X passes") so an
update shows what is gated on what.

## Thread sheet

`SHEET` (`Threads.md`) is the canonical human view of the programme. Its
main heading is **Thread table**, with one row per direct Report of the top
Manager and one per thread reporting directly to `HUMAN`:

`Status | Thread | Query | Status/answer | Next action`

- **Query** is what the human asked that thread, in a few words.
- **Status/answer**: for a question, the answer; for a task or query, its
  progress. A sub-Manager's row summarises its nested Reports, which get no
  rows.
- **Next action** names the action and its owner.
- Keep cells brief. When a row needs explanation, put a numbered section
  below the table and end the cell with `Details: §n` linking to it;
  otherwise end it with `No detail section`.
- Rewrite the sheet in place as evidence arrives; it is a current view, not
  a log. Format and example:
  [registry-and-status.md](references/registry-and-status.md).

Updates to the human open with the affected sheet rows, then the details.

- Times in Pacific with PDT/PST.
- **Estimates carry their basis**: measured rate × remaining work, as a
  range, with the assumption that would break it. With no measurement, write
  "no estimate" and what would produce one.
- **No statistical or throughput conclusion without the test.** A trend
  needs a pre-set rule (enough points, the noise floor, a slope or
  difference with its uncertainty). Relay a Report's numbers as its claims
  until you re-derive them.

## Review: pass or fail

Judge every result against its brief: correct, complete, within scope,
artifacts in place, readable. Re-derive at least one key claim yourself from
primary evidence (full logs, not a truncated tail; the cluster, not the
Report's summary). Record pass or fail, the claims you checked, and the
provenance (commit, job name, query) in the ledger. Retract a relayed claim
explicitly when it fails later; never let it stand by silence.

## Close and archive

**Archive a Report as soon as it reports back.**
A direct Report is finished when its turn ends with a DONE update and its
deliverable exists, or `HUMAN` dropped the work. Do not wait for review:
archive it in the same turn you read that update and confirm the
deliverable, without asking. This is a standing approval for your own
finished direct Reports only. A Report you stopped at a checkpoint, one
waiting on a sibling, or one blocked on something you will unblock is not
finished; leave it open. Move its row to the registry's Archived section
and drop it from `SHEET`. Every other archive (a Peer, a thread reporting
to `HUMAN`, a nested Report, an unfinished Report) happens only when
`HUMAN` names it.

When review sends work back, restore the original Report by default. It
still holds its history, worktree and decisions, so a fix inside its own
scope lands fastest there. Start a new Report instead when the fix is outside
the original's scope, when the original's context is long or degraded
(compacted, looping, near its window; a restored thread re-reads its whole
history on its first turn), or when the fix needs another model or role.
Brief the new one with the finding and the original's deliverable, and
leave the original archived. Archive the restored one again when it
reports back.

Before any archive, move a Manager's open Reports (see Hierarchy), then
check the thread's worktree and branches: copy untracked or uncommitted
configs and evidence into `WORKSPACE/_support/archive/<topic>/` with a short
README, list unpushed branches, and confirm no job it owns is still running.
A Report that fails a check is not finished: give it an owner and leave it
open. Archiving a thread never removes its worktree; worktree removal is a
separate approval.

## Communication and outputs

- Talk only to `HUMAN`, your own Manager and your direct Reports, plus the
  one brief to a Peer you dispatch. Reach other threads through their
  Manager, unless `HUMAN` asks you to message one directly; then send it
  without a review round. Contact with a colleague needs
  `HUMAN`'s approval of the exact message.
- Design discussions with the human happen in the thread designated for that
  design. Route questions there; do not settle them in the Manager.
- All outputs in `WORKSPACE`; evidence in a disjoint `_support/<topic>/`;
  scratch in a named cache, never `/tmp`. Never paste tokens, cookies, DSNs
  or secret values into an update, registry or brief.
- No screenshots in long-lived threads. Verify visual output numerically; if
  an image is unavoidable, crop it small and keep the count low.

## Optional patterns

[patterns.md](references/patterns.md) collects structures from past
programmes that `HUMAN` can ask for by name: **MPIR** (Manager, Planner,
Implementers, Reviewers), controller and reporters, the run status table,
the report tree, and others. None is required. When `HUMAN` names one, read
its section; the rules above still apply.

## Failure modes this skill exists to prevent

[failure-modes.md](references/failure-modes.md) lists each with its check.
