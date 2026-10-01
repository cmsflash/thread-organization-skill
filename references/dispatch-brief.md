# Dispatch briefs

A brief is the whole contract for a managed thread. It must stand alone: the
thread does not see the driver's history.

## Template

```text
Agent-drafted assignment from <DRIVER title> under <HUMAN>'s instructions.
You are managed by <DRIVER title>. Supersedes: <earlier brief or "none">.

Report to <DRIVER session> via the authenticated Host <origin>, fresh
requestId, mode queue, clientTimeZone <tz>. Prefix reports with your thread
name and "agent-drafted". Every report opens with your thread-sheet row:
Status | Thread | Query | Status/answer | Next action, where Next action
names its owner (same thread / <DRIVER> thread / named peer / <HUMAN>).

Task: <one paragraph: the question or change, and why>.
Done when: <testable criteria>.
Authorised: <exact actions, e.g. "one smoke job named atlas-smoke-*">.
Not authorised: <what needs the human: relaunches, merges, contacting people>.
Scope: may write <paths / branch / worktree / jobs>; must not touch <other
threads' worktrees, jobs, checkpoints>.
Model: <model · effort>; on <condition> switch to <fallback> (only if the
human authorised it).
Inputs: <existing evidence to reuse; do not re-gather>.
Outputs: <WORKSPACE>/<file>; evidence in <WORKSPACE>/_support/<topic>/.
Gates: <ordered steps; stop and report at each>.
Waiting: background waiters, no polling, no screenshots.
Blockers: report the exact error and evidence promptly; do not retry silently.
```

## Before sending

1. Resolve the exact session and read its latest reply.
2. Inspect its pending inbox. If an older item is still queued, either fold it
   into this brief and propose removing it, or hold this brief.
3. Check the scope against every other live brief for overlap.
4. Record the brief, model and returned prompt request ID in the round file.

## After sending

The returned `accepted` is inbox admission. The row moves to `running` only
when a running turn is observed, and to `reported` only on a final reply.

## Round file

`_support/dispatches/<yyyymmdd>_round.json` with `common` (shared contract
text) and `tasks[]` (`session`, `title`, `model`, `effort`, `text`,
`requestId`). Write it with a JSON encoder, not by hand: raw newlines inside
strings make it unparseable by strict readers.
