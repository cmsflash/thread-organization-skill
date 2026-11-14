# Autonomous log: Atlas, overnight window

Grant (`HUMAN`, <time PDT>): simple fixes and reruns of approved sweep
points until he takes over; anything that changes what a run measures
waits for him.

- <time PDT> Autonomous: told Atlas: Eval Sweep to rerun shards 9–12 of `atlas-eval-padfix`; reason: they failed on a node eviction, and a rerun of approved points is inside the grant.
- <time PDT> Autonomous: classified the loader's off-by-one on the last batch as a simple fix: pad to the batch size, plus a test. Directed Atlas: Loader Fix to add it on `fix/loader`; the sweep's remaining points wait for it.
- <time PDT> Autonomous: approved the relaunch as `atlas-eval-padfix-lastbatch` once `loader_pad_test` is green; reason: the fix has a test that reproduces the bug.
- <time PDT> Left for `HUMAN`: raising the eval timeout from 600 s to 900 s; reason: it changes what the sweep measures, so it is outside the grant.
- <time PDT> Autonomous: relayed the relaunch's 36/36 points to Atlas: Sweep Analysis; reason: its comparison was gated on the full sweep.
