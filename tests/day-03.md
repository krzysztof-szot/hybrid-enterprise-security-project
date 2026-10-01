# Day 03 — Test record

Executed by the owner on 2026-10-01. PASS applies only to the stated observation. [Implementation](../docs/day-03.md) · [Screenshots](../evidence/day-03/README.md) · [Text evidence](../evidence/day-03/console-excerpts.md).

| ID | Preconditions / procedure | Expected | Actual / status | Evidence |
|---|---|---|---|---|
| D03-01 | Auditing Success active; add adm.onprem to GG-Workstation-Admins | Membership and event 4728 | Both observed; actor Administrator — PASS | Screenshot 01 |
| D03-02 | Fresh adm.onprem workstation session, elevated PowerShell; identity and HKLM test | Admin token, create/delete key succeeds | True; key created, removal no displayed error — PASS | Screenshot 02 |
| D03-03 | Anna ordinary session; same HKLM creation | Access denied | PermissionDenied / SecurityException — PASS | Screenshot 03 |
| D03-04 | LAPS module available; update schema, grant computer SELF permissions, query attributes/rights | LAPS attributes and intended OU permissions | Seven attributes; SELF command succeeded; privileged rights holders listed — PASS for recorded preparation | Text evidence |
| D03-05 | LAPS GPO applied on workstation; inspect events and DC metadata | Local change, AD backup, encrypted source, authorized decryption | 10018/10020/10004; EncryptedPassword, Success, Domain Admins — PASS | Screenshot 04 and text |
| D03-06 | Working LAPS; Reset-LapsPassword then query metadata | Updated timestamp | 11:11:32 → 11:20:36; encrypted source and Success retained — PASS | Text evidence |
| D03-07 | Valid Anna credentials confirmed by Get-ADComputer; query LAPS using both credential parameters | No LAPS password data | ReturnedObjects=0 — PASS | Screenshot 05 and preceding successful AD query |
| D03-08 | DC audit GPO applied; auditpol and gpresult | Success for groups; Success/Failure for users; GPO present | Matched — PASS | Text evidence |
| D03-09 | Temporarily remove adm.onprem, restore in finally, query 4729 | Removal logged; membership restored | 4729 at 11:37:10; adm.onprem present afterward — PASS after repeat read | Corrected screenshot 06 and text |
| D03-10 | Existing KDS key, waiting period elapsed, RSAT installed; create/install gMSA | BFL-WKS01 sole configured retriever; test True | Matched — PASS for installation | Supplied creation query and Test-ADServiceAccount |
| D03-11 | Limited task principal; restricted output directory; manual start | Task completes as gMSA | LastTaskResult=0 at 21:17:40; bfl\gmsa-labtask$ in file — PASS | Screenshot 07 |

## Invalid/inconclusive attempts retained in troubleshooting

- Null Anna credential: LAPS result used administrative context; NOT a negative authorization test.
- Incorrect Anna credentials: authentication error; NOT an authorization denial.
- Immediate 4729 query: no matching event; later read found the event.
- Initial projected Get-KdsRootKey output: collection wrapper obscured fields; NOT proof that no key existed.

## Not performed

Unauthorized-host gMSA retrieval; gMSA rollover; explicit privileged-operation denial under the gMSA; LAPS recovery login using the retrieved password; scheduled 30-day rotation; post-authentication actions; immediate revocation of existing session tokens; exported EVTX/central monitoring. Functional success does not certify a complete least-privilege or production-hardening baseline.

## Final state

adm.onprem restored in GG-Workstation-Admins; registry test key removed; LAPS active for labadmin; BFL-gMSA-Test and output file retained without a recurring trigger.
