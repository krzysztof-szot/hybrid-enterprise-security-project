# Day 03 — Administrative identities, Windows LAPS, auditing and gMSA

Date: 2026-10-01. Recorded functional scope completed.

[Tests](../tests/day-03.md) · [Screenshots](../evidence/day-03/README.md) · [Text evidence](../evidence/day-03/console-excerpts.md)

## Objective and starting state

Extend the Day 02 configuration with a separate workstation administrator, managed local credentials, account/group auditing and a working managed service identity. The owner performed the changes. This record distinguishes command output, screenshots and owner confirmation; no tests were rerun while documenting.

BFL-DC01 runs Windows Server 2025; BFL-WKS01 runs Windows 11 Enterprise Evaluation. Initially adm.onprem belonged only to Domain Users, GG-Workstation-Admins was empty, and successful security-group changes were already audited.

## Workstation administration

Added adm.onprem to GG-Workstation-Admins, which the Day 02 GPO already placed in local Administrators on Workstations. Membership remained Domain Users plus GG-Workstation-Admins; Domain Admins was not assigned.

Event 4728 at 10:37:26 recorded Administrator adding the On-prem Admin account. On BFL-WKS01, an elevated PowerShell session identified itself as bfl\adm.onprem and IsInRole(Administrator) returned True. It created HKLM:\SOFTWARE\BFL-Day03-AdminTest, then removed it without a displayed error. Anna's ordinary session attempted the same creation and received PermissionDenied / SecurityException.

This proves the tested workstation operation and separation from Anna. It is not proof that every AD administrative operation is denied to adm.onprem. Local Domain Admins membership inherited from earlier configuration was retained.

## Windows LAPS

Initial inspection found the LAPS PowerShell module but no msLAPS-* attributes. Update-LapsADSchema completed, and seven attributes were subsequently listed. Set-LapsADComputerSelfPermission granted computers under Workstations permission to maintain their own LAPS data.

Find-LapsADExtendedRights on that OU returned SYSTEM, Domain Admins, Enterprise Admins and BUILTIN\Administrators. This was an OU permission review, not a forest-wide ACL audit.

GPO-BFL-Workstation-LAPS was linked to Workstations with these settings:

| Setting | Value |
|---|---|
| Backup directory | Active Directory |
| Managed local account | labadmin |
| Password length / complexity | 20 / 4 (upper/lower case, digits and special characters) |
| Password age | 30 days |
| Password encryption | Enabled |
| Authorized decryptors | Not configured; observed effective principal BFL\Domain Admins |
| Automatic account management | Disabled |

The existing labadmin password was replaced by LAPS. Subsequent workstation administration used adm.onprem.

Client events at 11:11:32 showed 10018 (AD password update), 10020 (local account update) and 10004 (processing success). A later 10016 meant no further update was needed. The effective-policy event 10021 also reported expiration protection enabled, encrypted history size 0, DSRM backup disabled, and post-authentication grace period 24 hours/actions 0x3. These defaults were observed, not separately functionally tested.

On the DC, Get-LapsADPassword metadata showed Account=labadmin, Source=EncryptedPassword, DecryptionStatus=Success, AuthorizedDecryptor=BFL\Domain Admins. Forced rotation using Reset-LapsPassword on the workstation advanced PasswordUpdateTime from 11:11:32 to 11:20:36. The displayed expiration shifted from 31 October 10:11:32 to 10:20:36; timestamps are retained as displayed, without treating the one-hour display difference as a failed rotation.

### Negative LAPS read test

The first Get-Credential attempt returned null. Get-ADComputer rejected the null credential, while the subsequent LAPS query returned data in the administrator context. That attempt was invalid as an Anna test. A later incorrect-password attempt also failed authentication and was not counted as an authorization denial.

The owner then supplied valid Anna credentials: Get-ADComputer returned BFL-WKS01. Get-LapsADPassword with both Credential and DecryptionCredential set to Anna returned zero objects. The evidence shows TestedIdentity=BFL\anna.finance and ReturnedObjects=0. No plaintext LAPS password was displayed. This establishes the tested account's inability to retrieve LAPS data, not a universal permission audit.

## Persistent audit configuration and removal event

GPO-BFL-DC-Auditing was linked to the built-in Domain Controllers OU:

- Advanced Audit Policy / Account Management / Security Group Management: Success.
- User Account Management: Success and Failure.
- Force audit policy subcategory settings to override category settings: Enabled (configured; effective registry value not separately queried).

auditpol confirmed both subcategories. gpresult listed Default Domain Controllers Policy, GPO-BFL-DC-Auditing and Default Domain Policy.

A controlled test removed adm.onprem from GG-Workstation-Admins and restored it in a finally block. The immediate event query returned no matching records; a subsequent wider query found event 4729 at 11:37:10. Final membership again included adm.onprem. The initial empty query was a timing/window observation, not proof that auditing failed. Existing-session privilege revocation was not tested.

## gMSA and task execution

Get-KdsRootKey initially produced a collection wrapper that displayed blank projected fields. Enumerating its contents revealed one existing root key created/effective at 11:11:31. Its creation mechanism was not established from supplied output. No additional key was created, deleted or backdated, and the system clock was not altered. The lab waited until the server reported 21:12:18 before creating the gMSA.

RSAT Active Directory tools were installed on BFL-WKS01; RestartNeeded=False. The account was then created in OU=Service Accounts,OU=BFL,DC=balticfinance,DC=test:

| Setting | Value |
|---|---|
| Account | gmsa-labtask / BFL\gmsa-labtask$ |
| DNSHostName | gmsa-labtask.balticfinance.test |
| Allowed password retriever | BFL-WKS01 computer object only |
| Kerberos encryption | AES128, AES256 |
| Enabled | True |
| Installation test | Install-ADServiceAccount succeeded; Test-ADServiceAccount=True |

The account was not added to Administrators. A test directory C:\LabTasks\gmsa-test was configured with SYSTEM/Administrators Full and gmsa-labtask Modify, with inherited permissions removed.

Task BFL-gMSA-Test used LogonType=Password, RunLevel=Limited and no scheduled trigger. Its action ran cmd.exe /d /c whoami, redirecting output to identity.txt. No manually entered service password was used. At 21:17:40, LastTaskResult=0 and the file contained bfl\gmsa-labtask$. This verifies execution under the gMSA; an explicit denied privileged operation under that service identity was not tested.

## Final state and limitations

adm.onprem remains a workstation administrator through its group. The registry test key was removed. LAPS manages labadmin. The gMSA, restricted output directory and manually triggered task remain for review. No recurring task trigger was configured.

The work did not test gMSA automatic password rollover, unauthorized-host password retrieval, LAPS password-based local recovery login, LAPS post-authentication actions, a 30-day scheduled rotation, or EVTX export/central collection. Day 04 uses a separate provisioning-agent gMSA; gmsa-labtask is not reused for synchronization.

## References

- [Windows LAPS policy settings](https://learn.microsoft.com/en-us/windows-server/identity/laps/laps-management-policy-settings)
- [Get-LapsADPassword credentials and decryption](https://learn.microsoft.com/en-us/powershell/module/laps/get-lapsadpassword)
- [KDS root key preparation](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/manage/group-managed-service-accounts/group-managed-service-accounts/create-the-key-distribution-services-kds-root-key)
- [New-ADServiceAccount](https://learn.microsoft.com/powershell/module/activedirectory/new-adserviceaccount)
