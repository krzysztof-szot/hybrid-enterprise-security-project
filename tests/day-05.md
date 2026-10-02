# Day 05 — Test record

**Date:** 2026-10-02. Tests were performed by the owner, not rerun during documentation.

Preconditions: the Day 04 Cloud Sync pilot is configured for three selected security groups; tst.sync is the controlled test identity. Provisioning tests below use **Provision on demand**.

| ID | Procedure and precondition | Expected result | Actual result | Status / evidence |
|---|---|---|---|---|
| D05-01 | Provision the initially excluded test identity and inspect detailed scoping | User skipped because scope fails | Source active=True; scoping evaluation=False; object skipped | PASS — [01](../evidence/day-05/01-sync-user-out-of-scope.png) |
| D05-02 | Add tst.sync to GG-IT and repeat provisioning | Cloud object created | Export reports user created, AccountEnabled=True and expected attributes | PASS — [02](../evidence/day-05/02-sync-user-after-scope-fix.png) |
| D05-03 | Inspect provisioning logs after creation | Successful Create from AD to Entra ID | BFL Sync Test; Create; Active Directory → Microsoft Entra ID; Success | PASS — [03](../evidence/day-05/03-sync-user-provisioning-log.png) |
| D05-04 | Query group membership before cleanup | Corrected identity remains in GG-IT | Domain Users and GG-IT returned | PASS — [console excerpt](../evidence/day-05/console-excerpts.md) |
| D05-05 | Disable tst.sync in AD and query Enabled | Enabled=False | tst.sync False | PASS — [console excerpt](../evidence/day-05/console-excerpts.md) |
| D05-06 | Provision the disabled identity while retaining scoped membership | Cloud AccountEnabled=False | Portal export reports user updated, AccountEnabled=False | PASS — [copied portal text](../evidence/day-05/console-excerpts.md) |

## Evidence interpretation

The initial screenshot's generic “Object is in scope” caption conflicts with its detailed Scoping filter evaluation passed=False. D05-01 relies on the detailed evaluation and skipped action. The identity is associated through the exercise context, not visible in that screenshot.

The initial missing membership is the controlled setup; no initial membership output is published. The final query directly confirms the corrected GG-IT membership. D05-06 is supported by copied export text, not a separate sign-in test or screenshot.

## Not tested

- Scheduled synchronization latency or background-cycle completion time.
- Password synchronization and login for tst.sync.
- Denied login after disabling or revocation of existing sessions.
- Removal from synchronization scope, deletion or restoration.
- A broader tenant-wide regression check.

PASS applies only to the specific results above. See [implementation](../docs/day-05.md) and [evidence inventory](../evidence/day-05/README.md).
