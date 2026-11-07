# Patterns

Structures that worked in past programmes, collected so `HUMAN` can ask for
one by name. **None is required.** Use one when `HUMAN` names it or the work
fits it; the rules in SKILL.md still apply inside every pattern. Collected
Oct 13, 2026 from the SBV training and SBV ratio estimation programmes. The
examples are their real files: `SBV/` is
`/Users/zhuoran/Workspaces/SBV training/` and `Ratio/` is
`/Users/zhuoran/Workspaces/SBV ratio estimation/`. Where an example differs
from SKILL.md (several written before Oct 8 say `mode queue`), SKILL.md
wins.

| pattern | use when | `HUMAN` asks with |
|---|---|---|
| [MPIR](#mpir) | a task splits into parallel slices of bulk work | "use MPIR", "planner-implementer-reviewer" |
| [Controller and reporters](#controller-and-reporters) | a costly run needs close watching | "a controlling thread with report threads in loops" |
| [Shared context, short briefs](#shared-context-short-briefs) | several Reports need the same background | — |
| [Run status table](#run-status-table) | `HUMAN` wants every run's state | "run status table", "non-run progress" |
| [Report tree](#report-tree) | `HUMAN` wants the hierarchy or archive candidates | "report tree", "agent tree", "who reports to me" |
| [Reporting directly to `HUMAN`](#reporting-directly-to-human) | `HUMAN` wants to work with a thread himself | "report to me directly", "transfer X to me", "discuss with me" |
| [Autonomy window](#autonomy-window) | `HUMAN` is away and allows routine decisions | "fix simple things until I wake up" |
| [Follow-on report](#follow-on-report) | a new deliverable copies an earlier one's form | "a similar report on X", "source context from Y" |
| [Subagents inside a Report](#subagents-inside-a-report) | `HUMAN` leaves the choice open | "a thread or a sub-agent, your choice" |
| [Dispatch-round folders](#dispatch-round-folders) | recording any dispatch round | — |
| [Reusable brief lines](#reusable-brief-lines) | writing a brief or a follow-up | — |

## MPIR

**M**anager → **P**lanner → **I**mplementers → **R**eviewers (the name is
Zhuoran's, Oct 13). Expensive models plan and check; cheap models do the
bulk work in parallel.

| role | count | tier | does | writes |
|---|---|---|---|---|
| Manager | 1 | expensive | creates every thread, opens each stage, integrates | the deliverable, or has the final reviewer write it |
| Planner | 1 | expensive | reconnaissance and the plan, not the bulk work | `plan.md` |
| Implementers | N, in parallel | cheap | one slice each, followed literally | its slice folder only |
| Reviewers | 1–2, in parallel | expensive | re-derive claims from primary data, draft sections | `review.md`, `section.md` |

Models in Oct 2026: the Manager on Opus 5.5, at low effort when Zhuoran set
it (Oct 10: "The manager thread should be Opus 5.5 Low"); the planner on
Opus 5.5 high (Oct 10) or max (Oct 13); reviewers on Opus 5.5 max;
implementers on `aire/glm-5.3`, `aire/glm-5.3-flash` and
`aire/ds-v4.1-flash` at max, one per slice, with the hardest slice on
`aire/glm-5.3`. Use the `aire/` routes, never `zai/`, which bills per token.
The Oct 13 run cost $58 for the Manager and $47 for the planner and both
reviewers together; the `aire/` implementers cost nothing per token.

Each stage is a dispatch round. The Manager opens the next stage only when
the last one's deliverables exist and hold up.

1. **Plan.** `plan.md` gives the definitions every slice uses, the
   inventory, example queries validated with row counts, exactly N slices,
   a review checklist per reviewer, and risks and open questions for
   `HUMAN`. Each slice has a disjoint write scope and one model, and is
   written for a cheaper model to follow literally: exact names, filters
   and windows; ordered steps; output files and their columns; stop
   criteria; a time cap; and what to do when a step returns nothing.
   Before opening the next stage, the Manager re-runs the plan's key
   queries itself and puts any fix in `amendments.md`, which overrides
   `plan.md` and goes to every later brief. On Oct 13 that check found the
   planner's per-minute query undercounting running sandboxes, before any
   implementer used it.
2. **Implement.** Each implementer follows its slice in order and saves
   every query or command as run, with its raw result, so a reviewer can
   re-run it. It never fills a gap by guessing or extrapolating, and it
   records where the plan was wrong.
3. **Review.** Each reviewer verifies against primary data, not the slice
   summaries, and records every claim with its source, its own query and a
   verdict: holds, corrected (with the right value) or unverifiable. It
   checks the method as well as the arithmetic (windows, attribution,
   aggregation, denominators, double counting), because cheap models can
   label a derived number measured or average averages. Corrections go in
   its own files, never an implementer's.
4. **Integrate.** The Manager re-derives a few headline numbers itself,
   saving each query and result under `_support/manager_checks/`, then
   assembles the reviewed sections into the deliverable, or has the final
   reviewer write it, and reports.

Only the Manager creates threads; the planner's brief says so. Titles name
the role or the slice: `X: Plan`, `X: Cluster Inventory`, `X: Final Review`.

Updates are what wake the Manager, so a Report that stops without sending
one leaves it idle. After opening a stage, arm a background turn waiter on
each of its threads (see Wait without polling), and when one settles,
check that its deliverable exists, then archive it at once, planner and
reviewers included (SKILL.md, Close and archive); review fixes go to the
restored implementer or a new one. On Nov 5 a Manager held seven finished
Reports open until Zhuoran asked why. On Oct 11, `Train: Colleague Run Sizes:
Final Review` (Opus 5.5 max) ended its turn on max-tokens at 8:38 AM PDT
with no report and no update. Its Manager had no waiter and stayed idle
until Train noticed, 38 hours later; rerun at high effort, the reviewer
finished in 19 minutes. On Oct 13, SBV Ratio Estimation armed a waiter per
Report and steered both reviewers to write up when they passed their
75-minute cap; all six Reports delivered within 4 hours 15 minutes of the
plan brief.

Lighter variant: for one code change, the Manager designs and reviews and
one cheap implementer builds (Train: Eval When Idle, Oct 7).

Examples: the whole `Ratio/` folder, a complete run (Oct 13, 6:12–10:23 PM
PDT): briefs and templates in `Ratio/_support/dispatches/`, with
`Ratio/_support/context.md`, `Ratio/_support/plan/amendments.md` and
`Ratio/_support/manager_checks/`; `SBV/_support/dispatches/20261010_run_sizes/brief.md`,
a sub-Manager briefed to run MPIR itself.

## Controller and reporters

For a costly run that needs close watching. Zhuoran, Oct 6: "an Opus 5.5
Max controlling thread but not in a low level loop, with a Sonnet 5.5 and a
Aire GLM-5.3 report threads in loops".

- **The controller** (expensive model) builds, launches, decides and fixes.
  It checks on a slow cadence, every 1–2 hours, and whenever a reporter
  sends an alert. It runs no tight loop itself.
- **Two reporters**, its Reports on two different models, are read-only.
  Each runs the same check script on its own ~10-minute checkpoints
  (agent-waking, as in Monitoring) and appends every check to its own log.
  It messages the controller only on success, an issue or a threshold
  crossing, repeats a condition only when it worsens or clears, and never
  coordinates with the other reporter.
- **The brief lists the triggers as numbers**, e.g. the job not RUNNING; a
  restart or OOM; no new step for 30 minutes; judge errors above 5 %; spend
  passing each $1,000, with a pause-and-ask at the budget; the reward
  slope's t crossing 2.
- **Two models** give an independent cross-check and cover when one stops:
  on Oct 7 the Sonnet reporter's last reviewed check was at 9:37 AM PDT,
  and the GLM reporter kept checking until 4:50 PM PDT.

Examples: `SBV/_support/dispatches/20261006_launch/brief_grader_controller.md`;
the reporter template `SBV/_support/grader_overfits/brief_reporter.md`.

## Shared context, short briefs

When several Reports in a round need the same background, put it in one
file and keep each brief to what differs.

- **The shared file** holds the question, definitions, known facts (marked
  "verify before relying on them"), access commands, rules and reporting
  instructions. Each brief opens "Read first: `<file>`. It is part of this
  brief." The brief and the file together are the whole contract.
- **Templates** for repeated roles (implementer, reviewer, reporter) take
  placeholders such as `{TITLE}`, `{MODEL}`, `{SLICE}`, `{DIR}` and `{CAP}`.
- **A correction lands in one place**, but a Report that has already read
  the file keeps the old text: steer it the change.

Examples: `Ratio/_support/context.md`;
`SBV/_support/dispatches/20261006_launch/common_rules.md` (standing rules
for run threads); `SBV/_support/dispatches/20261013_mimo6_report/common_context.md`
(shared by a report thread and a discussion thread).

## Run status table

`HUMAN`'s most frequent view request (four times, Oct 7–12), answered in
chat. It covers runs; the Thread table in `SHEET` covers threads.

```markdown
As of <time PDT, weekday Mon D>. **<headline, e.g. None of our runs are on the cluster.>**

**Runs**

| # | Run | Owner | State | Result / next |
|---|---|---|---|---|
| 1 | `<full run name>` | <thread> | 🔵 Running | <progress> |
| 2 | `<full run name>` | <thread> (→ you) | 🟢 Done Oct 11 4:33 AM | <result; GPU-h> |
| 3 | <next run> | <thread> | ⏸ Waiting on you | <the decision> |

**Non-run work**

| Thread | State | Result / next |
|---|---|---|

**Waiting on you**
1. **<decision>:** <options, the recommended one first>.
```

- Number the rows: `HUMAN` refers to them by number (on Oct 8, to rows
  16–19). Use full run names.
- State is an emoji plus words: 🔵 running, 🟡 approved or being set up,
  🟢 done, 🔴 failed or stopped, ⚫ skipped, ⏸ planned, on hold or waiting.
  Always write the words: in the Thread table's legend, 🔵 means waiting on
  `HUMAN`.
- Owner is the thread; mark threads that report to `HUMAN` "(→ you)".
- Collapse a finished series into one row, and drop rows `HUMAN` no longer
  needs ("we can drop it from further reports", Oct 8).
- Runs others started in the same namespace get a short separate table,
  state only.

## Report tree

Give the view asked for.

**Management tree** ("list your currently managed report tree"): an ASCII
tree rooted at the Manager, with nested Reports marked "(managed by X)",
then a table `Thread | Status | Next action (owner)`. State the count, e.g.
"**Only 1 of 10 is working.** Every other next action is a decision from
you."

**Archive tree** ("recommend archival candidates directly on the tree"):
rooted at `HUMAN`, so threads that moved to him appear as "(→ you)"
branches, with a mark and a one-line reason on every node.

```text
🗄️ archive · ✅ keep · ⏳ archive later

Zhuoran
├── Train (me) ✅
│   ├── Train: Stack Smokes 🗄️ ......... smoke11–17 done, results saved
│   └── Train: Colleague Run Sizes ✅ ... survey not finished
│       └── …: Survey Plan ⏳ ........... its Manager's call, after the report
└── Train: Grader Overfits (→ you) ✅
```

Never mark a thread that reports to `HUMAN`, or anything under it, for
archiving (Zhuoran, Oct 12: "We shouldn't archive any thread reporting to
me"); nested Reports are their own Manager's call. End with the count and
the exact question ("That leaves 6 to archive now: … Should I archive
them?"). Finished direct Reports should already be archived (SKILL.md,
Close and archive); archiving anything else on the tree still needs
`HUMAN`'s approval of that set.

**Reporting to `HUMAN`** ("which threads report to me"): a table
`Thread | Session | Since | Its own Reports`, with temporary moves marked.

## Reporting directly to `HUMAN`

`HUMAN` may want a thread for himself, "for the time being" or for good.

- **Created for `HUMAN`:** a Peer. The brief opens "You report directly to
  `HUMAN`"; his messages are its instructions. It gets no Manager prefix
  (Zhuoran, Oct 7: "Strip your prefix and tell it to report to me") and
  never reports back to the thread that created it (Peer brief in
  dispatch-brief.md).
- **Moved to `HUMAN`:** steer the thread one message saying that it now
  reports to `HUMAN`, temporarily or not; his messages are its
  instructions; its own Reports stay with it; it archives nothing without
  his say; its rules are unchanged; and, if the move is temporary, its old
  Manager resumes on handback. Moved threads have kept their titles.
- **In the registry:** list it under "Reporting directly to `HUMAN`" with
  its session, when it moved and whether the move is temporary. The old
  Manager stops instructing it, still shows it in its views marked
  "(→ you)", and never proposes archiving it.
- **Discussion partner:** a thread created for `HUMAN` to think with. It
  opens with a one-paragraph read of the results, the 3–5 questions or
  tensions that matter most and an offer to dig into any of them, then
  waits. It works read-only and proposes, with its cost, anything that
  needs GPUs. When another thread is writing up the same results, it tells
  `HUMAN` if the discussion changes a conclusion.

Examples: `SBV/_support/dispatches/20261010_run_sizes/transfer.args.json`;
`SBV/_support/dispatches/20261013_mimo6_report/brief_discuss.md`.

## Autonomy window

`HUMAN` lets the Manager decide a named class of actions while he is away.
Zhuoran, Oct 10: simple fixes and new ladder experiments are allowed "until
I wake up and take over"; tricky fixes wait for him.

- The grant names what it covers, what it does not, and when it ends.
  Anything outside it still needs `HUMAN`, as Authority describes.
- Before acting, the Manager states the decision's class ("classified … as
  a simple fix: restore the pre-bump order, plus a test"), then acts
  through its Reports. A fix still comes with a test that reproduces the
  bug.
- Each decision gets one log line with its reason, in the round's
  `autonomous_log.md`: `<time> Autonomous: <decision>; reason: <why>`.
  Decisions left for `HUMAN` are logged too.

Example: `SBV/_support/dispatches/20261009_smokes/autonomous_log.md`.

## Follow-on report

"A similar report on X" (Zhuoran, Oct 13): a new Report modelled on an
earlier deliverable, with context from the thread that owns X.

- **Model it on the earlier deliverable:** match its structure and its
  standards (traceable numbers, figures checked against source data, a
  Slack-ready summary), and name the thread that built it, so questions
  about its method can go through the Manager.
- **Context-only questions:** when `HUMAN` asks for it, the brief may let
  the Report ask the owning thread directly, even one outside its chain.
  It asks for context only and never instructs that thread or changes its
  work. The brief lists every thread the Report may contact.

Example: `SBV/_support/dispatches/20261013_mimo6_report/brief_grader_report.md`.

## Subagents inside a Report

"Dispatch an agent" always means a Report. A Report may still use a
subagent for a short, self-contained piece of its own work when `HUMAN`
leaves the choice open (Zhuoran, Oct 5: "You can choose another top-level
thread under your management, or you can choose a sub-agent. It's your
choice") or suggests one (Oct 8: "Let Main Migration manage it, potentially
via a subagent"). The Report owns the result and covers it in its own
updates. Use a Report instead when `HUMAN` may want to read or message the
helper, or the work will take follow-ups.

## Dispatch-round folders

One folder per round, `_support/dispatches/<yyyymmdd>_<topic>/`, as an
alternative to the single round file in dispatch-brief.md:

- `brief_<name>.md`: the brief as written;
- `<name>.args.json`: the exact `session/prompt` request, written with a
  JSON encoder. Every later message to that Report gets one too
  (`<name>_go.args.json`, `transfer.args.json`);
- `<name>.session`: the new Report's session ID;
- the round's shared files (`common_context.md`) and, as needed,
  `round.tsv`, `renames.tsv` and `autonomous_log.md`.

The folder then shows what each Report was told, when, and in which mode.

## Reusable brief lines

- "Read first: `<file>`. It is part of this brief."
- "Keep each reasoning step short: long max-effort steps hit the 5-minute
  stream-idle timeout."
- "Label every number M (measured), D (derived, arithmetic shown or
  scripted) or A (assumed)."
- "Save every query to `<dir>/queries/` as run, with its raw result next to
  it. A reviewer re-runs them."
- "When a step returns nothing, check its pieces separately and record what
  you found. Don't guess a value or extrapolate from other runs; leave the
  cell empty and say why."
- "Time cap: ~60 minutes of agent time. At the cap, send what you have and
  what remains."
- "If an analysis needs GPUs, propose it with its cost; don't run it."
- "No contact with anyone. Put questions for `HUMAN` in your output."
- "Colleagues' runs: size and layout only (GPUs, sandboxes, CPU, memory,
  timings, model name)."
- After an infra failure: "Continue. Your turn ended at `<time>` on
  `<error>`. That's not a fault in your work. Pick up where you left off and
  report at your next milestone."
- After a max-tokens stop: steer "Continue; write `<deliverable>` now, keep
  the reasoning short", or drop the effort from max to high.
- Past the time cap: "You're past the cap. Stop verifying and write up now
  with what you have. Mark unchecked items `not checked (time)`."
