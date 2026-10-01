# Day 02 — Domain workstation, Group Policy and access control

Date: 2026-10-01. Status: recorded configuration and tests completed; ten screenshots published.

[Tests](../tests/day-02.md) · [Screenshot evidence](../evidence/day-02/README.md) · [Console excerpts and owner observations](../evidence/day-02/console-excerpts.md)

## Objective and environment

Join a Windows workstation to Baltic Finance AD, apply scoped policies, and test allowed and denied access. The owner performed all lab changes; this document records the supplied results, not a new automated run.

| Component | Recorded configuration |
|---|---|
| Client | BFL-WKS01; Windows 11 Enterprise Evaluation; reported OS version 10.0.26300 |
| VM allocation | 4 GB RAM, 2 vCPU, 64 GB growable disk; owner-confirmed setup |
| Client networking | VMnet8 NAT; DHCP address observed as 192.168.58.129/24; gateway 192.168.58.2 |
| Client DNS | Ethernet0 manually set to 192.168.58.10 |
| Domain and DC | balticfinance.test; BFL-DC01 at 192.168.58.10 |
| Computer OU | OU=Workstations,OU=Computers,OU=BFL,DC=balticfinance,DC=test |
| Local administration | labadmin; separate from ordinary domain sessions |

The client was introduced before the original Day 06 milestone because Day 02 requires real domain-client tests. It is AD joined; Entra join and Intune enrollment have not been performed.

## Configuration and verification

### Domain join and ordinary-user logon

The workstation was renamed BFL-WKS01 and joined to balticfinance.test. The computer query showed PartOfDomain=True and Test-ComputerSecureChannel returned True. Anna logged on as BFL\anna.finance, with BFL-DC01 as LOGONSERVER and GG-Finance in her medium-integrity user token. Her PasswordLastSet became 2026-10-01 07:43:22 rather than the initial pwdLastSet=0. Sara also completed an ordinary-user logon.

### Domain password and lockout policy

Configured in Default Domain Policy; verified using Get-ADDefaultDomainPasswordPolicy.

| Setting | Before | After |
|---|---|---|
| Minimum password length | 7 | 14 |
| Complexity | Enabled | Enabled |
| Password history | 24 | 24 |
| Minimum / maximum age | 1 / 42 days | 1 / 42 days |
| Lockout threshold | 0 | 10 failed attempts |
| Lockout duration / observation window | 10 / 10 minutes | 15 / 15 minutes |
| Reversible encryption | Disabled | Disabled |

The existing 42-day maximum age was retained; this is a recorded lab configuration, not a claim of a complete modern password baseline.

A dedicated temporary account, tst.lockout, was created under Users/Security. After establishing successful authentication with its test password, the owner confirmed ten intentional incorrect attempts. Security event 4740 identified tst.lockout and caller BFL-WKS01 at 09:26:11. Correct-password access was subsequently confirmed by the owner, and Disable-ADAccount left Enabled=False. Automatic expiration of the lockout interval was not separately tested.

### Workstation security GPO

GPO-BFL-Workstation-Security is linked to BFL/Computers/Workstations, Link Enabled=Yes, Enforced=No, Security Filtering=Authenticated Users.

- Interactive logon: Machine inactivity limit = 900 seconds.
- Windows Firewall Domain, Private and Public profiles = enabled.
- Default inbound action = Block; default outbound action = Allow.

Client gpresult listed the GPO, the registry returned InactivityTimeoutSecs=900, and ActiveStore firewall output returned True/Block/Allow for all three profiles. This verifies configuration, not a network traffic matrix.

For the negative scope test, BFL-WKS01 was temporarily moved to the parent Computers OU. After refresh, gpresult listed only Default Domain Policy. The workstation was then restored to Workstations; final output confirmed the security GPO applied again. Absence from gpresult does not prove that every previously applied setting was removed.

The functional inactivity test was repeated with guest display and sleep timers temporarily disabled to avoid confusing suspend with session locking. After approximately 15 minutes, Anna's desktop changed to the lock screen and a password was required. The owner confirmed restoring the original power settings afterward. This test is an owner observation, without a dedicated screenshot or lock-event export.

### Finance SMB share and authorization

For this resource-limited lab, Finance is hosted on the DC at C:\LabShares\Finance. A production deployment should separate the file-server role.

| Layer | Principal | Permission |
|---|---|---|
| SMB share | BFL\Domain Admins | Full |
| SMB share | BFL\GG-Finance | Change |
| NTFS | SYSTEM and BUILTIN\Administrators | Full control |
| NTFS | BFL\GG-Finance | Modify, inherited by child files/folders |

NTFS inheritance was removed from the share folder. Supplied output showed an additional explicit Administrators Full entry alongside its inheritable entry. Share EncryptData=True was verified; negotiated session encryption was not independently inspected.

Anna created and read anna-access-test.txt through the UNC path. Sara received Access is denied when listing the same share. These are separate allow and deny tests; drive visibility alone is not authorization enforcement.

### Finance drive mapping

GPO-BFL-Finance-Drive is linked to BFL/Users with Authenticated Users filtering. User Configuration → Preferences → Windows Settings → Drive Maps contains:

- F: → \\BFL-DC01.balticfinance.test\Finance; label Finance; Reconnect enabled.
- Action Replace, associated with Remove this item when it is no longer applied.
- Item-level targeting: user is a member of BFL\GG-Finance.
- Run in logged-on user's security context was ultimately unchecked.
- Apply once was not enabled; the item was not disabled.

After the targeting repair below, Anna's net use F: returned Status OK and reading F:\anna-access-test.txt succeeded. Sara's separate session returned NET HELPMSG 2250 for F:, as expected. Removal after changing an existing user's group membership was not tested.

### Local administrator management

GPO-BFL-Workstation-LocalAdmins is linked to Workstations. Computer Configuration → Preferences → Control Panel Settings → Local Users and Groups uses Update on Administrators (built-in), adding BFL\GG-Workstation-Admins. Neither Delete all member users nor Delete all member groups was selected.

After computer policy refresh, membership was:

- BFL\Domain Admins
- BFL\GG-Workstation-Admins
- BFL-WKS01\Administrator
- BFL-WKS01\labadmin

Final gpresult listed GPO-BFL-Workstation-Security, GPO-BFL-Workstation-LocalAdmins and Default Domain Policy. GG-Workstation-Admins remains empty; adm.onprem was not granted privileges. No actual administrative action by a member of that group was tested.

## Troubleshooting

### Initial client setup

Set-DnsClientServerAddress initially failed with PermissionDenied and succeeded in an elevated shell. A later DNS timeout was followed by successful TCP 53 connectivity and an SOA response from the DC; the transient timeout's root cause was not established. AD cmdlets were unavailable on the client, so directory queries were run on the DC.

### Sleep confused the first lock test

The guest display turned off after five minutes and VMware later offered Resume this VM. A password prompt after resume did not establish that the inactivity policy caused the lock. The repeat test disabled guest sleep/display timers temporarily, observed a lock after 15 minutes, required Anna's password, and restored the timers. Recorded original AC/DC values were display 5/3 minutes and sleep 15/10 minutes.

### GPO applied but F: did not exist

**Problem:** gpresult listed GPO-BFL-Finance-Drive, but net use F: returned 2250. Anna could read the UNC path directly and her token contained GG-Finance.

**Investigation:** confirmed OU link, Authenticated Users filtering, user-based targeting and enabled item. A fresh logon and unchecking the user-context option did not resolve it. Queries found no matching events in the selected windows; that absence did not establish success.

**Configuration defect:** the stored Drives.xml filter had a group name but an empty SID:

~~~xml
<FilterGroup bool="AND" not="0" name="BFL\GG-Finance" sid="" userContext="1" primaryGroup="0" localGroup="0"/>
~~~

**Resolution:** reselect GG-Finance through the group picker and Check Names, save, and refresh user policy.

**Verification:** F: then mapped successfully and the file could be read; Sara remained without F:. This supports the unresolved group reference as the cause. The repaired XML was not separately supplied, so a post-repair SID value is not reproduced.

## Outcome, limitations and next step

The recorded Day 02 tests demonstrate domain join, GPO scope, lockout logging, session locking, share authorization, group-targeted mapping and managed local-group membership.

Remaining limits include one DC, a lab share on that DC, no GPO backup/restore test, no exported EVTX logs, no firewall traffic tests, and no privileged-member execution test. Replace mapping can reconnect a drive during refresh; application impact was not tested. Existing Domain Admins and local administrators were retained. No cloud services or LAPS were deployed.

Day 03 should address separate administrative identities, least privilege, Windows LAPS, service-account design and auditing, beginning with the actual current state.
