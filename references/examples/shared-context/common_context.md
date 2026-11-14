## Context: `atlas-retrain-padfix` and the runs it builds on

Agent-drafted by Atlas (`session-<uuid>`) for `HUMAN`, <time PDT>. Shared
by Atlas: Retrain Report and the discussion thread Retrain Results.

**What the run is:** the baseline training recipe on the fixed data loader.
- **Data:** the 3,000-task training split, with a 120-task held-out eval.
- **Recipe:** unchanged from `atlas-retrain-base`: 400 updates, a save
  every 50.
- **Code:** branch `fix/loader`, checked by `loader_pad_test`.

**Read first:**
- `<WORKSPACE>/atlas-retrain-padfix_result.md`: headline results, held-out
  series, compute used.
- `<WORKSPACE>/atlas-retrain-base_result.md`: the baseline it is compared
  with.
- `<WORKSPACE>/sweep_comparison.md`: the eval sweep before and after the
  fix.
- **Data:** per-update metrics through `python3 tools/q.py "<SQL>"`.

**Key numbers (verify against the result docs):**
- **Loss:** 1.92 → 1.41 (first vs last 20 updates), slope t 6.1.
- **Held-out (120 tasks):** 0.61 at the start, 0.67 at update 400.
- **Compute:** 58 GPU-hours.

  | | base | padfix |
  |---|---|---|
  | final held-out | 0.63 | 0.67 |
  | GPU-hours | 55 | 58 |

**Caveats to state honestly:**
- one seed per run;
- held-out scores have about ±0.04 standard error, so the two finals are
  within noise of each other;
- the sweep comparison covers eval only, not training.
