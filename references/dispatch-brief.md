# Dispatch briefs

A brief is the whole contract for a Report. It must stand alone: the Report
does not see its Manager's history.

## Template

```text
Assignment from <MANAGER title> under <HUMAN>'s instructions.
You are a Report of <MANAGER title>, titled <the Report's title>.
Supersedes: <earlier brief or "none">.

Send updates only to <MANAGER session>, via the authenticated Host <origin>,
fresh requestId, mode steer (never queue), clientTimeZone <tz>, using the
deepseek-harness-thread-management skill and its message header. Below the
header, every update opens with your thread-sheet row: Status | Thread |
Query | Status/answer | Next action, where Next action
names its owner (same thread / <MANAGER> / named sibling / <HUMAN>). Message no
other thread except Reports this brief lets you manage. A direct message
from <HUMAN> wins over this brief: act on it, then tell <MANAGER> what you
did.

Task: <one paragraph: the question or change, and why>.
Done when: <testable criteria>.
Authorised: <exact actions, e.g. "one smoke job named atlas-smoke-*">.
Not authorised: <what needs the human: merges, contacting people, others'
jobs; jobs this Report launches stay its own to fix, relaunch or delete>.
Own Reports: <"none", or "up to N, for <purpose>; their parent set to you
and titled <Task>, or <your title>: <Task> for one that cannot be nested;
they update only you and get no authority beyond this brief">.
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
First reply: <if your first reply goes to HUMAN: "Open your first turn's
final answer, the message after your last tool call, by repeating or
summarising the questions or tasks this brief gives you."; otherwise omit>.
```

## Peer brief

A Peer reports to `HUMAN`, not to the thread that dispatched it. Use the
template above with these changes:

- Title it `<Task>` and give it no parent; a Peer never takes `PREFIX`.
- Replace the first two lines with "You report directly to <HUMAN>; his
  messages are your instructions."
- Drop "Supersedes" and the whole update paragraph. Never ask the Peer to
  send the dispatcher updates, summaries, results or a done message: it
  answers `HUMAN` in its own thread, and blockers go to `HUMAN` too.
- Its own Reports are `HUMAN`'s call; "Not authorised" items wait for him.
- Always keep the "First reply" line: its first reply goes to `HUMAN`.

Wrong: "Send Train a summary when done." Right: say nothing about Train.

## Detached Report brief

A detached Report is placed like a Report and briefed like a Peer. Use the
Peer brief with these changes:

- Nest and title it as for any Report, then sync its detached Host marker
  with the expected parent before selecting its model and sending the brief.
  Follow [attachment and Host sync](attachment.md) if either step fails;
  preserve the detached brief and record the mismatch.
- After "You report directly to <HUMAN>; his messages are your
  instructions." add: "You are a detached Report of <MANAGER title>: you
  sit under it in the thread list, but you send it nothing and it does not
  instruct you. If <HUMAN> attaches you, <MANAGER title> will say so in one
  message that names where your updates go; from then on you are its
  Report."

To detach a Report that is already working, which is what `HUMAN` taking it
over for a while means, steer it one message: "From now on you report
directly to <HUMAN>; his messages are your instructions.", then the quoted
paragraph above, then "Your task, scope, rules and own Reports are
unchanged, and you archive nothing without his say." To attach one, which
is what a handback means, steer it one message: "<HUMAN> has attached you
to <MANAGER title>: from now on you are its Report.", then the template's
update paragraph, which names where its updates go. Sync the corresponding
Host marker and update `REGISTRY` separately; a successful message does not
confirm the marker, and a successful marker write does not deliver this
instruction. Only `HUMAN` authorizes either change; see
[attachment and Host sync](attachment.md) for partial failure handling.

## Before sending

1. For a new Report, create a top-level thread with `session/create` in the
   Manager's own DSH project (Host workspace), then nest and title it in the
   order SKILL.md's Dispatch gives. For an existing one, resolve the exact
   session and read its latest reply.
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
