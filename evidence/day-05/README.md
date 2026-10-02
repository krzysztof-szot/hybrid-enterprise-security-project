# Day 05 — Evidence inventory

**Session:** 2026-10-02. Three owner-supplied screenshots document the scope issue, correction and successful provisioning. Unnecessary tenant and object identifiers are masked in the images.

## 01 — Initial scope failure

Detailed scoping shows source active=True and filter evaluation=False; the final action was skipped. The generic “Object is in scope” caption is inconsistent with these details. The test identity is established by the session context.

![Initial failed scope evaluation](01-sync-user-out-of-scope.png)

## 02 — Creation after scope correction

Provisioning after adding tst.sync to GG-IT created bfl.tst.sync in Entra ID. Export details show AccountEnabled=True and the expected name, IT department and description.

![Successful creation after scope correction](02-sync-user-after-scope-fix.png)

## 03 — Successful provisioning log

BFL Sync Test has action Create, source Active Directory, target Microsoft Entra ID and status Success. The portal displays 2026-10-02 06:31:32 with local date display selected.

![Successful provisioning log](03-sync-user-provisioning-log.png)

## Supplemental text

[Console and portal excerpts](console-excerpts.md) record retained GG-IT membership, AD Enabled=False, and the subsequent cloud export AccountEnabled=False.

[Implementation](../../docs/day-05.md) · [Tests](../../tests/day-05.md)
