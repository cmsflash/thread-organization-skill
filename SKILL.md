---
name: thread-driver
description: "Drives a programme of managed DeepSeek Harness agent threads from one driver thread: adopts and names threads, keeps a durable registry of exact sessions, writes scoped dispatch briefs, picks the cheapest model that holds up, waits without polling, reviews results pass/fail, and keeps a thread table whose every next action names its owner. Use when one thread coordinates several others. Triggers: 'drive these threads', 'dispatch a thread for', 'adopt this thread', 'status of the threads', 'who is waiting on what', 'update the thread table', 'step down the ladder', 'archive the finished threads'."
---

# Drive managed threads

One **driver** thread owns a programme and the threads working on it. This
skill is the management discipline. For every Host API call — login, list,
read a reply, prompt, rename, archive, wait — use
`deepseek-harness-thread-management`; this skill never restates transport.

## Programme parameters

Read these from the programme's workspace before acting; never hard-code them:

| parameter | meaning |
|---|---|
| `PREFIX` | thread-title prefix, e.g. `Atlas: ` |
| `WORKSPACE` | the one folder for every programme artifact |
| `SHEET` | the human-facing thread sheet, `WORKSPACE/Threads.md` |
| `REGISTRY` | the durable thread registry file in `WORKSPACE` |
| `LADDER` | model-ladder rules and ledger, if the programme uses one |
| `DRIVER` | the driver's exact session ID and Host origin |
| `HUMAN` | the person who approves; nobody else's approval counts |

If one is missing, ask once and record the answer in `REGISTRY`.

## Authority

The human's approval is scoped to an exact action, target and content. Do not
widen it, and do not ask again for what was clearly approved.

- **Needs approval of the specific action:** deleting, replacing, relaunching
  or scaling a live job, even inside an approved plan; archiving a thread;
  removing a queued message; answering a thread's pending approval or
  question; any contact with another person (review requests, mentions, DMs,
  comments); commits, pushes and PRs where the repo requires confirmation.
- **Approved once, then executed:** when the human approves "archive A, B, C",
  archive A, B, C without re-asking. A fourth target, a different resource,
  or a broader action is a new request.
- **Approvals disabled in the harness is a rejection, not permission.** A
  rejected action stays undone and becomes a blocked row.
- A managed thread inherits only what its brief grants. It never self-authorises
  a config change after a failure; it reports and waits.

## Registry: the durable truth

Keep one row per managed thread in `REGISTRY`: exact full session ID, title,
model and effort, owner scope, write scope, current task, state, next-action
owner, last verified time. Format and example:
[registry-and-status.md](references/registry-and-status.md).

- **Exact sessions only.** Resolve and store the full `session-…` ID. Never act
  on a title or a truncated ID; titles collide and change.
- **Last known is not current.** A registry row says what was true when it was
  verified. Before any decision or report that depends on runtime state, read
  the current state (running flag, latest committed reply, job status) and
  label anything not re-read as "last verified <Pacific time>".
- Update the row at every state change, and update the file's "last updated"
  line when you do. Remove or strike superseded rows instead of adding a
  second row for the same thread.

## Adopt a thread

When an existing thread comes under management: confirm the exact session,
read its latest reply, rename it to `PREFIX<Task>` (keep the old title in the
registry), record its model, scope, open work, worktree and unpushed branches,
then add it to `REGISTRY`, `SHEET` and the ladder ledger. Renaming is an approved write
only when the human asked for adoption.

## Dispatch

Default to delegating. The driver plans, reviews and relays; managed threads do
the work. Brief template and a filled example:
[dispatch-brief.md](references/dispatch-brief.md).

- **One owner per area.** Route follow-up work to the thread that already owns
  the area. Open a new thread only when the owner's history would dominate the
  cost, or the work is independent.
- **Disjoint write scopes.** Each brief names the files, worktree and cluster
  resources the thread may change. Two threads never share a worktree they
  build from; a thread that builds images gets its own
  `<repo>/.worktrees/<name>`.
- **Dispatch independent work together**, in one round, each with its own
  brief. Record the round (briefs, models, prompt request IDs) in `WORKSPACE`.
- **Before sending to a thread, inspect its pending inbox.** A still-queued
  older brief will run after your new one. Make the new brief say what it
  supersedes and propose removing the stale item; do not just append.
- **Prompt accepted means inbox admission only.** Record the request ID; the
  thread has not started until a running turn is observed.

## Choose the model

Use the programme's ladder when it has one; rules in
[model-ladder.md](references/model-ladder.md). In short: step down one rung per
task while work holds up, change rung only between tasks, keep high-risk work
one rung above the floor, and honour any model the human names.

- **Verify availability before relying on a switch.** After changing a
  thread's model, confirm its next turn actually started on that model.
- **A provider or Host failure is not a quality verdict.** Billing errors,
  "model unavailable" and transport timeouts before any work are infra rows:
  record the exact error, do not step the thread up or down for it, and follow
  only the fallback the human authorised.
- Default effort `high`. Use `max` only when asked or the task needs it, and
  keep that thread's context short; long-context `max` turns hit stream-idle
  timeouts.

## Wait without polling

- Long waits run as **background waiters** (the RPC skill's turn waiter, or a
  job watcher) that survive across turns and notify on settlement. Track every
  job ID; collect or kill each one before closing your turn.
- A watcher must treat **every** terminal state as terminal: success, failure,
  cancellation, eviction, deletion. One that only matches success hangs forever
  on a failure.
- No repeated status checks that re-read a long history. A check cadence, if
  any, is long (about hourly) and does real work each time.
- **When the human pauses or stops the programme**, kill waiters, do not
  re-arm them, do not dispatch, and leave queued items alone until told.
  Pausing means no further turns, including "just checking" ones.

## States: say exactly which one

Never collapse these. Each has its own evidence:

| layer | state | evidence |
|---|---|---|
| thread | drafted → approved → submitted | prompt accepted (inbox admission) |
| | running → reported | running turn observed → `turn/end` with a final reply |
| | reviewed | driver verdict recorded |
| job | submitted → admitted | resource accepted → pods scheduled and placed |
| | running → working | processes up → steps or trials advancing in its metrics |
| | checkpoint saved | tracker or manifest committed, not a directory existing |
| | terminal | scheduler reports SUCCEEDED / FAILED / deleted |
| experiment | complete | terminal job **and** results checked against pre-set criteria |

A thread's turn ending is not its job ending. A job RUNNING is not training. A
goal is complete only when its objective holds, not when an agent reports.

## Blockers and dependencies

Every open row's next action names exactly one **owner**:

- **Same thread** — the managed thread itself is working.
- **`DRIVER` thread** — the driver owes the next action.
- **`<peer>`** — a named other managed thread.
- **`HUMAN`** — a decision or approval; state the exact question.

When the wait is on an external system (scheduler, provider, CI), name the
owner who acts once it clears and the system it is waiting for, e.g. "Same
thread: rerun once CI is green".

An interruption, error or rejection becomes an explicit **blocked** row at
once, with the exact error text and where it was observed. Do not retry
silently, and do not spend more than one short attempt diagnosing before
reporting. Record dependencies between rows ("starts after X passes") so a
report shows what is gated on what.

## Report: the thread sheet

`SHEET` (`Threads.md`) is the canonical human view of the programme.
Its main heading is **Thread table**, with one row per thread:

`Status | Thread | Query | Status/answer | Next action`

- **Query** is what the human asked that thread, in a few words.
- **Status/answer**: for a question, the answer; for a task or query, its
  progress.
- **Next action** names the action and its owner.
- Keep cells brief. When a row needs explanation, put a numbered section
  below the table and end the cell with `Details: §n` linking to it;
  otherwise end it with `No detail section`.
- Rewrite the sheet in place as evidence arrives; it is a current view, not a
  log. Format and example:
  [registry-and-status.md](references/registry-and-status.md).

Reports to the human open with the affected sheet rows, then the details.

- Times in Pacific with PDT/PST.
- **Estimates carry their basis**: measured rate × remaining work, as a range,
  with the assumption that would break it. With no measurement, write "no
  estimate" and what would produce one.
- **No statistical or throughput conclusion without the test.** A trend needs
  a pre-set rule (enough points, the noise floor, a slope or difference with
  its uncertainty). Relay a managed thread's numbers as its claims until you
  re-derive them.

## Review: pass or fail

Judge every result against its brief: correct, complete, within scope,
artifacts in place, readable. Re-derive at least one key claim yourself from
primary evidence (full logs, not a truncated tail; the cluster, not the
thread's summary). Record pass or fail, the claims you checked, and the
provenance (commit, job name, query) in the ledger. Retract a relayed claim
explicitly when it fails later; never let it stand by silence.

## Close and archive

Archive only threads the human named. Before proposing an archive, check the
thread's worktree and branches: copy untracked or uncommitted configs and
evidence into `WORKSPACE/_support/archive/<topic>/` with a short README, list
unpushed branches, and confirm no job it owns is still running. Archiving a
thread never removes its worktree; worktree removal is a separate approval.

## Communication and outputs

- Talk only to the human and the managed threads. Anyone else needs the
  human's approval of the exact message.
- Mark every message you send to a thread as agent-drafted, from the driver.
- Design discussions with the human happen in the thread designated for that
  design. Route questions there; do not settle them in the driver.
- All outputs in `WORKSPACE`; evidence in a disjoint `_support/<topic>/`; scratch
  in a named cache, never `/tmp`. Never paste tokens, cookies, DSNs or secret
  values into a report, registry or brief.
- No screenshots in long-lived threads. Verify visual output numerically; if
  an image is unavoidable, crop it small and keep the count low.

## Failure modes this skill exists to prevent

[failure-modes.md](references/failure-modes.md) lists each with its check.
