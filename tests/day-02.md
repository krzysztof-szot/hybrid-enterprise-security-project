# Day 02 — Test record

Executed by the owner on 2026-10-01. PASS is limited to the stated check. Screenshots, supplied console output and owner observations are distinguished below; these were not rerun while writing documentation.

[Implementation](../docs/day-02.md) · [Screenshots](../evidence/day-02/README.md) · [Text evidence](../evidence/day-02/console-excerpts.md)

| ID | Preconditions and procedure | Expected | Actual result / status | Evidence |
|---|---|---|---|---|
| D02-01 | Client joined and restarted; query Win32_ComputerSystem and Test-ComputerSecureChannel | Correct name/domain; healthy channel | BFL-WKS01, balticfinance.test, True / True — PASS | Screenshot 01 |
| D02-02 | Anna interactive logon; whoami, LOGONSERVER, whoami /groups | Domain identity, DC, Finance membership | bfl\anna.finance, BFL-DC01, GG-Finance, medium integrity — PASS | Screenshot 02 |
| D02-03 | Updated Default Domain Policy; Get-ADDefaultDomainPasswordPolicy | Length 14; threshold 10; duration/window 15 minutes | Matched; remaining values documented — PASS for configuration | Screenshot 03 |
| D02-04 | Computer in Workstations; gpupdate and gpresult | Security GPO applies | Security GPO listed — PASS | Screenshot 04 |
| D02-05 | Security GPO applied; registry and ActiveStore queries | Inactivity 900; all firewall profiles True/Block/Allow | Matched — PASS for effective configuration | Supplied text; console excerpts |
| D02-06 | Move computer to parent Computers OU; refresh and gpresult | Workstation security GPO absent | Only Default Domain Policy listed — PASS for scope exclusion | Screenshot 05; no assertion of complete setting rollback |
| D02-07 | Restore computer to Workstations; refresh | Security GPO applies again | Final gpresult lists it — PASS | Final computer-policy excerpt |
| D02-08 | Anna session; guest sleep/display timers temporarily disabled; wait without input | Lock after ~15 minutes; authentication required | Lock screen appeared after 15 minutes; password required — PASS, owner observed | Owner statement; no screenshot/event export |
| D02-09 | Dedicated tst.lockout; successful baseline authentication; ten deliberate wrong passwords | Account lockout and event 4740 | Owner confirmed count; 4740 identifies account and caller — PASS | Screenshot 06 and owner confirmation |
| D02-10 | Recover access, then disable temporary test account | Successful correct-password access; Enabled=False | Access owner-confirmed; disabled state shown — PASS with mixed evidence | Console excerpts; no independent auto-unlock timing test |
| D02-11 | Finance share and ACLs configured; Anna creates and reads file via UNC | Write/read allowed | Text written and read — PASS | Screenshot 07 |
| D02-12 | Separate Sara logon; list Finance via UNC | Access denied | UnauthorizedAccessException / Access is denied — PASS | Screenshot 08 |
| D02-13 | Repair mapping group reference; refresh Anna user policy | F: maps to Finance and file is readable | Status OK and expected text — PASS after remediation | Screenshot 09; Anna identity established in session, not visible in this image |
| D02-14 | Sara user session; refresh; whoami and net use F: | No Finance drive mapping | bfl\sara.security; 2250/no connection — PASS | Console excerpts |
| D02-15 | LocalAdmins GPO linked to Workstations; refresh; query local Administrators by SID | Add GG-Workstation-Admins, preserve original three entries | Four expected entries — PASS | Screenshot 10 |
| D02-16 | Final elevated client gpresult /scope computer /r | Security, LocalAdmins and Default Domain Policy applied | All three listed — PASS | Console excerpts, 10:20:29 |
| D02-17 | Share created; Get-SmbShare and Get-SmbShareAccess plus icacls | EncryptData=True; intended share/NTFS permissions | Matched — PASS for configuration | Supplied output summarized in console excerpts |

## Not tested or not fully established

- Actual administrative operations by a GG-Workstation-Admins member: NOT RUN; group remains empty.
- Automatic unlock after 15 minutes: NOT RUN as an isolated timed test.
- Firewall traffic allowance/denial and negotiated SMB session encryption: NOT RUN.
- Drive removal after withdrawing an existing user's group membership: NOT RUN.
- Complete removal of persistent settings after a computer leaves GPO scope: NOT VERIFIED.
- Backup/restore, GPO rollback and security behavior after suspend: NOT VERIFIED.
- Post-repair Drives.xml SID value: NOT CAPTURED; mapping behavior passed after reselection.
- Initial DNS timeout root cause: UNRESOLVED; later connectivity and name resolution succeeded.
- Windows LAPS, Entra join, synchronization and Intune: NOT IMPLEMENTED in Day 02.

## Final state

BFL-WKS01 is back in Workstations. Original guest display/sleep timers were restored (owner confirmation). tst.lockout is disabled. Finance share and its test file remain. All three computer GPOs apply; Anna has F:, Sara does not. No credentials are included.
