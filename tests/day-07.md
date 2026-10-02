# Day 07 — Test record

**Date:** 2026-10-02. Owner-executed tests; no lab commands were rerun during documentation.

Preconditions: BFL-WKS02 is Entra joined and Intune managed; Anna is the signed-in user; the assigned pilot device group contains BFL-WKS02.

| ID | Procedure | Expected result | Actual result | Status / evidence |
|---|---|---|---|---|
| D07-01 | Correct session-lock group from excluded to included; sync and inspect report | Device and setting succeed | One successful device; inactivity-limit setting Succeeded | PASS — [01](../evidence/day-07/01-session-lock-profile-summary.png), [02](../evidence/day-07/02-session-lock-profile-status.png) |
| D07-02 | Read InactivityTimeoutSecs locally | 300 seconds | REG_DWORD 0x12c | PASS — [owner-confirmed text](../evidence/day-07/console-excerpts.md) |
| D07-03 | Leave unlocked session idle without Win+L | Automatic lock after five minutes, reauthentication required | Owner observed five minutes and PIN/password requirement | PASS — owner observation |
| D07-04 | Reload edge://policy | Both SmartScreen policies true and mandatory | Platform / Device / Mandatory / OK for both | PASS — [03](../evidence/day-07/03-edge-smartscreen-policies.png) |
| D07-05 | Open normal site and Microsoft phishing demonstration in Edge | Normal site works; demo blocked without bypass | Normal browsing owner-confirmed; blocked continuation visible | PASS — [04](../evidence/day-07/04-edge-smartscreen-block.png) plus owner confirmation |
| D07-06 | Assign tailored baseline to pilot and inspect report | Successful processing without reported error/conflict | One Success; zero errors and conflicts | PASS — [05](../evidence/day-07/05-windows-security-baseline-status.png) |
| D07-07 | Restart Windows; sign in as Anna; open normal site | Login and browsing remain functional | Both confirmed by owner | PASS — owner confirmation |
| D07-08 | After restart, inspect Edge policies and repeat idle-lock test | Both true/OK; five-minute lock remains | All confirmed by owner | PASS — owner confirmation |

## Troubleshooting record

The first profile report was empty despite Sync Completed. The pilot group was mistakenly excluded. After correcting assignment, device and setting results succeeded. No policy recreation was required.

The first evidence file was initially named as a registry capture; it actually shows the profile summary and is now correctly named 01-session-lock-profile-summary.png.

## Not independently verified

- Every individual baseline control, LSA runtime protection and generated audit events.
- Final full baseline export, including hidden dependent settings.
- Snapshot creation or restore.
- A separate out-of-scope endpoint test or standard-user elevation test.
- SmartScreen blocked-site behavior repeated after baseline (policy values and normal browsing were rechecked).
- Custom compliance and Conditional Access; MDE onboarding; dedicated Day 09 security tests.

Baseline Succeeded is a deployment result, not comprehensive security certification.

[Implementation](../docs/day-07.md) · [Evidence](../evidence/day-07/README.md)
