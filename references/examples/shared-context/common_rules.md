# Standing rules for Atlas run threads

Agent-drafted by Atlas (`session-<uuid>`) for `HUMAN`.

- **Manager:** send updates only to Atlas (`session-<uuid>`) with
  `session/prompt`: mode `steer` (never `queue`), fresh `requestId`,
  `clientTimeZone` `<tz>`, through the `deepseek-harness-thread-management`
  skill and its message header. A direct message from `HUMAN` wins over
  anything relayed: act on it, then tell Atlas what you did.
- **Monitoring is agent-waking, not a loop:** at every checkpoint you wake,
  review one fresh check and decide (continue, diagnose, fix, kill,
  relaunch, or ask `HUMAN`), then arm the next. One managed background job
  per checkpoint; it wakes early if the job leaves RUNNING or a pod
  restarts. Cadence: 5 minutes, then 30 minutes, then hourly.
- **On a crash of a run you launched:** find the root cause, fix it with a
  test that reproduces it, relaunch under the next free run name and say
  what you did. If the cause is unclear, or the fix would change what the
  run measures, stop and send the exact question for `HUMAN`.
- **Cleanup:** delete your own run's leftover pods and volumes promptly.
  Delete only what your run created.
- **Names:** `atlas-<stream>-<what changed>`. Write full run names in
  updates, never shorthand.
- **Titles:** Title Case. A thread you manage is `<Your Title>: <Task>`.
- **Times:** Pacific. **Contact:** nobody besides Atlas and `HUMAN`.
- **Updates:** one concise update per result, blocker or decision, opening
  with your thread-sheet row.
