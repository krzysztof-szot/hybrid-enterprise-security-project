# Day 04 — Test record

Executed by the owner on 2026-10-01. PASS means the stated check matched expectations. OWNER-CONFIRMED results were reported by the owner and are not independently exported portal logs.

[Implementation](../docs/day-04.md) · [Screenshots](../evidence/day-04/README.md) · [Text evidence](../evidence/day-04/console-excerpts.md)

| ID | Preconditions / procedure | Expected | Actual / status | Evidence |
|---|---|---|---|---|
| D04-01 | Existing tenant inventory; check four proposed UPNs and local mail/proxyAddresses | No collision for prefixed identities | Four UPNs absent (owner); local mail/proxyAddresses empty — PASS with mixed evidence | Supplied AD output; owner check |
| D04-02 | Add tenant suffix and update four AD UPNs | bfl. prefix and tenant suffix; local names unchanged | Final table matched — PASS | Screenshot 01; tenant portion redacted |
| D04-03 | Inspect old sync and disable bfl.local | Old configuration disabled; Connect Sync off | Owner confirmed disabled/off and old agent inactive — OWNER-CONFIRMED PASS | Text record |
| D04-04 | Create cloud-only Hybrid Identity Administrator; sign in and register MFA | Working administrative identity | Owner confirmed account, role, login and MFA — OWNER-CONFIRMED PASS | Text record |
| D04-05 | Agent prerequisites; install/configure on DC | New agent Active | BFL-DC01.balticfinance.test active — PASS | Screenshot 02 and preflight text |
| D04-06 | Save selected security-group scope | Finance, IT and Security only | Three intended DNs displayed — PASS for configuration | Screenshot 03; pilot restriction noted |
| D04-07 | Provision Anna on demand by DN | In-scope user created with prefixed UPN | Export reports new Entra object created — PASS | Screenshot 04 |
| D04-08 | Provision adm.onprem on demand; verify imported GUID and scope details | Admin excluded | Correct AD account; Scoping filter evaluation passed=False; object skipped — PASS using detailed evidence | Screenshot 05 and GUID-to-user query; generic summary contradictory |
| D04-09 | Enable configuration; inspect new users | All four prefixed identities present | Owner confirmed all four; Anna sync enabled=Yes visible — PASS with mixed evidence | Screenshot 06 plus owner confirmation |
| D04-10 | Private browser session; new Anna UPN and current AD password | Cloud login succeeds | Owner confirmed success — OWNER-CONFIRMED PASS | No sign-in log export |
| D04-11 | Check synchronized group source/members | Finance: new Anna/Peter; IT: new Adam; Security: new Sara | Owner confirmed all correct — OWNER-CONFIRMED PASS | Text record |
| D04-12 | Search users after full sync | adm.onprem and tst.lockout absent | Owner confirmed absence — OWNER-CONFIRMED PASS | Text record |
| D04-13 | Inspect original cloud Anna/Peter | Remain cloud-only | On-premises sync enabled=No for both, owner confirmed — OWNER-CONFIRMED PASS | Text record |
| D04-14 | Inspect configuration status/logs | No reported errors | Owner confirmed expected status and no errors — OWNER-CONFIRMED PASS | Exact final status label/export not captured |

## Not proven / remaining checks

- IE ESC restoration: REQUESTED, NOT CONFIRMED.
- Password login for all four users, subsequent password-change propagation and latency: NOT TESTED; only Anna's login reported.
- Agent binary version, exact agent gMSA object/ACLs, local service status and deletion threshold: NOT CAPTURED.
- Failover, full tenant matching audit, exports of provisioning/sign-in logs: NOT PERFORMED.
- Device join, Intune, password writeback and new Conditional Access policies: NOT IMPLEMENTED in this stage.

The initial JoinNotFound result alone was not accepted as exclusion evidence. The later detailed filter result and owner-confirmed absence support the final test outcome.
