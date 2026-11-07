# thread-organization

A thread can use other top-level DeepSeek Harness threads as a more powerful
substitute for subagents. The **Manager** thread delegates to **Reports**:
full threads in the project's thread list that the human can watch and
message directly, that outlive the Manager's turns, take follow-up tasks, and
can manage Reports of their own. This skill is the management discipline for
that arrangement. It sits on top of `deepseek-harness-thread-management`,
which owns every Host API call; this skill never restates transport.

Terms:

- **Manager**: the managing thread.
- **Report**: a managed thread. Each Report has one Manager.
- **Hierarchical management**: a Report may manage Reports of its own.
  Titles gain one prefix level per Manager, e.g.
  `Train: Main Migration: Review Loop`.

The parts that are easy to get wrong:

- **"Dispatch an agent" means a Report**: a top-level thread, never a
  subagent.
- **Each level talks to the next.** A Manager instructs only its direct
  Reports; a Report updates only its own Manager. The human may message any
  thread, and that message wins over relayed rules.
- **Exact sessions, current state.** Act on full `session-…` IDs, never
  titles. A registry row is "last verified at …", not current; re-read the
  runtime before a decision that depends on it.
- **States are not interchangeable.** A prompt accepted is inbox admission. A
  turn ending is not a job ending. A job RUNNING is not training. A directory
  existing is not a committed checkpoint.
- **Always steer, never queue.** Send every message to another thread with
  `mode: steer`, so it lands at the target's next step instead of waiting for
  its turn to end.
- **Inspect the inbox before sending.** An older brief still queued runs after
  the new one unless the new brief supersedes it.
- **Infra is not quality.** A billing error, a rejected model or a transport
  timeout before any work keeps the Report's ladder rung.
- **A Peer does not report back.** A Peer is a top-level thread that reports
  to the human, outside the dispatcher's chain and without its title prefix.
  Its brief never asks it to send the dispatcher summaries or results, and
  the dispatcher does not instruct it afterwards.
- **First reply restates the brief.** A Peer, or a Report whose first reply
  goes to the human, opens that turn's final answer, the message after its
  last tool call, by summarising what it was dispatched to do. Text written
  before the tool calls is folded into the work steps and doesn't count.
- **Every next action has one owner**: the same thread, the Manager, a named
  sibling Report, or the human with the exact question.
- **Approval is scoped and only narrows down the chain.** Execute exactly what
  was approved; a new target or a broader action needs a new approval.
  Approvals disabled in the harness means rejected, not permitted. A job a
  thread launched stays its own to fix, relaunch or delete without asking.
  Owning a PR covers neither merging it nor resolving a human reviewer's
  thread.
- **Archive Reports as soon as they report back.** A Manager archives a
  direct Report in the turn its DONE update arrives and its deliverable
  exists, without waiting for review or asking, after saving its evidence.
  Review fixes go to the restored Report, or to a new one when the fix is
  out of scope or the old context is worn out. Any other archive still
  needs the human to name the thread.
- **No polling, no screenshots** in long-lived threads. Background waiters
  treat every terminal state as terminal.
- **Monitoring is the exception**, and only for training runs or a process
  the human explicitly asks to monitor: the thread wakes at every checkpoint
  (5 min, then 30 min, then hourly), decides what to do, and only then arms
  the next. An unattended loop that only logs checks does not count.

Programme specifics (title prefix, workspace, sheet, registry, ladder, the
Manager's own Manager, the human who approves) are parameters read from the
programme's workspace. The human-facing view is `WORKSPACE/Threads.md`,
whose **Thread table** has the columns
`Status | Thread | Query | Status/answer | Next action`.

## Usage

```
dispatch an agent to investigate the judge refusals
dispatch this to the TIS thread
adopt this thread
give a table of threads managed by you
who is waiting on what
keep monitoring large 8
archive the finished threads
use MPIR to estimate the GPU:CPU ratio
give me a run status table
show your report tree with archive candidates
temporarily transfer this thread to my reports
```

References: `references/registry-and-status.md` (registry and thread sheet
formats), `references/dispatch-brief.md` (brief template and round file),
`references/model-ladder.md` (ladder rules), `references/failure-modes.md`
(each failure with its check), `references/patterns.md` (optional named
patterns from past programmes, such as MPIR, the run status table and the
report tree).

## Install

Symlinked into the central skill store:

```bash
ln -s /Users/zhuoran/Programs/skills/thread-organization-skill ~/.agents/skills/thread-organization
```

One link, not two: `~/.claude/skills` is itself a symlink to `~/.agents/skills`
on this machine, so a second `ln -s` into it overwrites the first and leaves a
self-referential loop.
