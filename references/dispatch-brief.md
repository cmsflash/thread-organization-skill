# Dispatch briefs

A brief is the whole contract for a Report. It must stand alone: the Report
does not see its Manager's history.

## Template

```text
Assignment from <MANAGER title> under <HUMAN>'s instructions.
You are a Report of <MANAGER title>, titled <MANAGER title>: <Task>.
Supersedes: <earlier brief or "none">.

Send updates only to <MANAGER session>, via the authenticated Host <origin>,
fresh requestId, mode steer (never queue), clientTimeZone <tz>, using the
deepseek-harness-thread-management skill and its message header. Below the
header, every update opens with your thread-sheet row: Status | Thread |
Query | Status/answer | Next action, where Next action
names its owner (same thread / <MANAGER> / named peer / <HUMAN>). Message no
other thread except Reports this brief lets you manage. A direct message
from <HUMAN> wins over this brief: act on it, then tell <MANAGER> what you
did.

Task: <one paragraph: the question or change, and why>.
Done when: <testable criteria>.
Authorised: <exact actions, e.g. "one smoke job named atlas-smoke-*">.
Not authorised: <what needs the human: relaunches, merges, contacting people>.
Own Reports: <"none", or "up to N, for <purpose>; titled <your title>:
<Task>; they update only you and get no authority beyond this brief">.
Scope: may write <paths / branch / worktree / jobs>; must not touch <other
threads' worktrees, jobs, checkpoints>.
Model: <model · effort>; on <condition> switch to <fallback> (only if the
human authorised it).
Inputs: <existing evidence to reuse; do not re-gather>.
Outputs: <WORKSPACE>/<file>; evidence in <WORKSPACE>/_support/<topic>/.
Gates: <ordered steps; stop and send an update at each>.
Waiting: background waiters, no polling, no screenshots.
Monitoring: <training runs or a process HUMAN asked to monitor: "wake at
every checkpoint and decide (5 min, then 30 min, then hourly); one managed
background job per checkpoint, never an unattended loop"; otherwise omit>.
Blockers: send the exact error and evidence promptly; do not retry silently.
```

## Before sending

1. For a new Report, create a top-level thread with `session/create` in the
   Manager's own DSH project (Host workspace) and title it `PREFIX<Task>`.
   For an existing one, resolve the exact session and read its latest reply.
2. Inspect its pending inbox. If an older item is still queued, either fold
   it into this brief and propose removing it, or hold this brief.
3. Check the scope against every other live brief for overlap.
4. Record the brief, model and returned prompt request ID in the round file.

## After sending

The returned `accepted` is inbox admission. The row moves to `running` only
when a running turn is observed, and to `delivered` only on a final reply.

## Round file

`_support/dispatches/<yyyymmdd>_round.json` with `common` (shared contract
text) and `tasks[]` (`session`, `title`, `model`, `effort`, `text`,
`requestId`). Write it with a JSON encoder, not by hand: raw newlines inside
strings make it unparseable by strict readers.
