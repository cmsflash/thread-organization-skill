# Model ladder

The rungs, prices and models live in the programme's `LADDER` file because
they change. This reference holds only the rules.

## Rules

1. A new Report starts at the programme's starting rung.
2. When a task holds up, the Report's next task runs one rung lower.
3. When it does not, try another model on the same rung; when every model on
   the rung fails, step back up. That rung is the Report's floor for that
   kind of work.
4. Change rung only between tasks, never mid-task.
5. Each Report keeps its own position.
6. High-risk work (live jobs, training code, checkpoints) stays one rung above
   the floor until that rung has passed a low-risk task.
7. A model the human names overrides the ladder for that task.
8. Effort is a smaller dial: drop `max` → `high` before dropping a rung.

## "Holds up"

Correct (Manager re-derived one key claim), complete, within scope,
artifacts in place, readable. One miss on low-risk work: try the rung's
other model. A miss on high-risk work: step up now.

## Not a verdict

Record these as infra and keep the rung: billing or credit errors, Host
"model unavailable", transport errors before any work, harness-wide
interruptions, a history made unloadable by images. Name the exact error.

## Switching

After a switch, confirm the next turn ran on the new model before recording
the rung. Do not assume a model in the catalog is reachable or funded.

## Checkers

At milestones the human will act on, a checker Report on a mid rung, `high`
effort, gets the brief, the write-up or diff, and a short list of claims. It
verifies; it does not redo the work.

## Cost discipline

Long histories and polling cost more than model price. Keep the Manager's
turns short, compact when long, and use fresh Reports when an owner's
history would dominate a new task.
