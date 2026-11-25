# Report attachment and Host sync

Only `HUMAN` decides to attach or detach a Report; its Manager carries out that
instruction. A parent link, marker, completed task, adoption, transfer, archive,
or missing icon never grants authority to change attachment. The Report's
instructions and `REGISTRY` remain the behavioral record. The Host stores a
separate marker for display: a person icon **before** a detached title, with
tooltip `Detached: reports to you`. It is an indicator, not a toggle.

## Carry out an instruction

1. Resolve the exact Report, its Manager and Host, and the current human
   instruction. Read its parent and marker through
   `deepseek-harness-thread-management`. Do not infer attachment from a title.
2. Record the human-directed state and pending steps in `REGISTRY`. For a
   working Report, steer the [detach or attach message](dispatch-brief.md#detached-report-brief)
   with the required message header; retain its request ID. For a new detached
   Report, nest and title it first, sync the detached marker, then send the
   detached brief after selecting its model. Do not send an attached brief
   while a detached dispatch is being prepared.
3. Sync the marker using that skill's `set-attachment.mjs`, with the exact
   expected current parent and explicit attach or detach action. For a working
   Report this follows the steering message. Verify the returned target,
   parent and marker, not merely a zero exit code. Do not use `set-parent` or
   workspace membership `detachSession` to change Report attachment.
4. Finish the registry move and record message admission, observed behavior,
   and marker sync separately. Move a detached Report under "Reporting
   directly to `HUMAN`"; an attached Report belongs in the managed table.
   Update the thread sheet. Report any unfinished step with one next-action
   owner instead of calling the operation complete.

Detaching changes no title or parent. Stop directing, waiting on, reviewing or
archiving that Report as soon as the human directs detachment. An attach
message names the Manager and where updates go; do not infer resumed behavior
from a marker alone. Neither action changes the Report's own Reports.

## Partial failures

- **Marker written, message not admitted:** the icon is not delivery. Keep the
  human-directed state, mark delivery pending or unknown, and reconcile the
  message by its request ID before resending. Do not send new managed work
  under an attach whose instruction has not reached the Report.
- **Message admitted, marker not confirmed:** admission is not application of
  the instruction, and it is not marker sync. Record the prompt receipt and
  the Host error or unknown marker state. A missing icon cannot undo the
  human's detach, and a stale icon cannot veto the human's attach.
- **Unsupported Host:** preserve the requested behavior and registry state;
  record `marker sync: unsupported` visibly, with the exact refusal. Continue
  the authorized brief rather than silently reverting it. Never rewrite Host
  storage, recreate the Report, or switch to an unrelated endpoint.
- **Unknown write outcome:** use fresh Host state to reconcile before any
  retry. A delayed write can settle after a readback; never replay against a
  changed parent or a superseded human instruction. A parent mismatch stops
  the marker write, not a reason to reparent the thread automatically.
- **Registry update fails:** keep the receipts in the task's evidence and
  report the incomplete record. Do not reverse a delivered instruction or a
  confirmed marker to conceal the failure.

## Lifecycle cases

| Case | Required behavior |
|---|---|
| New attached Report | Nest, title, select model, send attached brief. A linked, unmarked Report is attached; no detached flag is needed. |
| New detached Report | Only on `HUMAN`'s request: nest first, sync detached marker, and send detached brief. If nesting or marker sync fails, preserve the detached instruction and make the UI mismatch visible. |
| Attach / handback | Only on `HUMAN`'s word: steer the update destination, sync attached marker with expected parent, and move the registry row. |
| Adoption / transfer | Set the new parent on the authorized adoption; reparenting preserves detachment. Carry the recorded attachment forward unless `HUMAN` also directs a change. An explicitly attached handover includes the attach message and marker sync. |
| Transferred registry row | Old Manager makes no parent or marker write. New Manager owns synchronization; the old Manager must not restore stale state. |
| Permanent move to `HUMAN` | Clear the parent on the authorized move to Peer; the Host removes the detached flag. Send the Peer instruction and update the registry. Do not set an attachment marker on an unparented Peer. |
| Lost-link restore | Follow [Restoring parent links](registry-and-status.md#restoring-parent-links): restore eligible parents first, then reconcile markers to the last human-directed attachment recorded there. This repairs display state, not reporting behavior; do not send attach/detach instructions or invent missing intent. |
| Archive / restore | Both retain parent and detached marker. Keep the archived row's kind. Do not clear a marker for archive or attach a detached Report when restoring it. Its Manager still needs `HUMAN`'s exact approval to archive a detached Report. |
| Sub-Manager | No descendant cascade. Each Report keeps its own attachment; detaching its Manager does not detach it. |

A restore instruction covers only synchronization of the recorded human intent
for its authorized targets. If the registry cannot establish that intent, or
the live state shows a conflicting transfer or newer instruction, stop that
row, report the mismatch and ask `HUMAN`; never choose attachment autonomously.
