# Brief: Atlas: Padfix Retrain

Assignment from **Atlas** (`session-<uuid>`), under `HUMAN`'s instructions.
You are its Report, titled **Atlas: Padfix Retrain**. Supersedes: none.
**Read `<WORKSPACE>/_support/common_rules.md` first.** Model: Fable, xhigh.
You are a **controller**, not a low-level monitor.

## Your run (approved by `HUMAN`)

- **`atlas-retrain-padfix`:** the baseline recipe on the fixed loader,
  400 updates on 16 GPUs, a save every 50 updates.

## The structure `HUMAN` asked for

1. **You** build, launch, decide and fix. You check results on a **slow**
   cadence, every 1–2 hours, and whenever a reporter sends an alert. You
   run no tight loop yourself.
2. **Two reporter threads you create and manage**, on two different
   cheaper models, one of them free or self-hosted:
   `Atlas: Padfix Retrain: Reporter A` and `…: Reporter B`. Brief each from
   `brief_reporter_template.md`.
   - Each wakes every ~10 minutes, runs the same read-only check and
     appends it to its own log.
   - It messages **you** only on success, an issue or a threshold crossing.
   - Reporters are **read-only** and never coordinate. Two models give you
     an independent cross-check: note where they disagree.
3. **You update Atlas**, mode `steer`, at launch, at the run's result, and
   on any blocker or decision for `HUMAN`.

## Before launch

Write `_support/padfix_retrain/check.py`, the one check both reporters run:
job state, restarts, updates done, last update time, loss over the last 10
updates and its slope, eval errors, saves.

## Readouts

Loss slope with its t; held-out score at each save against the baseline
run; GPU-hours. Write `<WORKSPACE>/atlas-retrain-padfix_result.md`.

## Scope

Your branch and worktree `.worktrees/padfix-retrain`, your run and its
leftovers, your two reporters, `_support/padfix_retrain/` and your result
doc. **Own Reports:** the two reporters only.
