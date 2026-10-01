# thread-driver

One DeepSeek Harness thread (the driver) runs a programme of work through
several managed threads: it adopts and names them, writes their briefs, picks
their models, waits on them, reviews what they report, and keeps the human's
view of who is waiting on what. This skill is that management discipline. It
sits on top of `deepseek-harness-thread-management`, which owns every Host API
call; this skill never restates transport.

The parts that are easy to get wrong:

- **Exact sessions, current state.** Act on full `session-…` IDs, never
  titles. A registry row is "last verified at …", not current; re-read the
  runtime before a decision that depends on it.
- **States are not interchangeable.** A prompt accepted is inbox admission. A
  turn ending is not a job ending. A job RUNNING is not training. A directory
  existing is not a committed checkpoint.
- **Inspect the inbox before sending.** An older brief still queued runs after
  the new one unless the new brief supersedes it.
- **Infra is not quality.** A billing error, a rejected model or a transport
  timeout before any work keeps the thread's ladder rung.
- **Every next action has one owner**: the same thread, the driver, a named
  peer, or the human with the exact question.
- **Approval is scoped.** Execute exactly what was approved; a new target or a
  broader action needs a new approval. Approvals disabled in the harness means
  rejected, not permitted.
- **No polling, no screenshots** in long-lived threads. Background waiters
  treat every terminal state as terminal.

Programme specifics (title prefix, workspace, sheet, registry, ladder, the
human who approves) are parameters read from the programme's workspace. The
human-facing view is `WORKSPACE/Threads.md`, whose **Thread table** has the
columns `Status | Thread | Query | Status/answer | Next action`.

## Usage

```
drive these threads
dispatch a thread for the eval sweep
adopt this thread
status of the threads / who is waiting on what
update the thread table
archive the finished threads
```

References: `references/registry-and-status.md` (registry and thread sheet
formats), `references/dispatch-brief.md` (brief template and round file),
`references/model-ladder.md` (ladder rules), `references/failure-modes.md`
(each failure with its check).

## Install

Symlinked into the central skill store:

```bash
ln -s /Users/zhuoran/Programs/skills/thread-driver-skill ~/.agents/skills/thread-driver
```

One link, not two: `~/.claude/skills` is itself a symlink to `~/.agents/skills`
on this machine, so a second `ln -s` into it overwrites the first and leaves a
self-referential loop.
