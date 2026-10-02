# Day 09 — Endpoint security on BFL-WKS02

**Session:** 2026-10-02. **Status:** recorded pilot scope completed, including optional MDE onboarding, Tamper Protection and manual BitLocker recovery-key rotation.

[Tests](../tests/day-09.md) · [16 screenshots](../evidence/day-09/README.md) · [Session excerpts](../evidence/day-09/console-excerpts.md)

## Objective and starting state

Manage endpoint protection through Intune and validate selected controls with positive and negative tests while preserving Day 08 compliance and Conditional Access.

BFL-WKS02 is the separate Entra-joined, Intune-managed Windows 11 workstation used by Anna Finance. The owner initially found no policies in the inspected Endpoint security categories. Real-time protection, cloud-delivered protection and automatic sample submission were already On; Tamper Protection was Off. All three firewall profiles were On, Public was active, and C: already had BitLocker On with a recovery-key entry available.

The pilot assignment group was BFL-Intune-Pilot-Devices. Windows profiles were used. The initial Windows (ConfigMgr) wizard exposed collections instead of Intune groups and was abandoned; there is no evidence that a ConfigMgr policy was deployed. Existing Day 07 baseline exceptions reserved this dedicated endpoint-policy scope.

The owner performed the configuration and tests through the portals and Windows UI. This document records the session, not a new automated execution or a complete policy export.

## Policies and resulting state

| Policy | Purpose | Recorded outcome |
|---|---|---|
| BFL-WIN-Defender-AV-Pilot | Managed antivirus | Published report: 1 Succeeded; quick scan and test-file quarantine observed |
| BFL-WIN-Firewall-Pilot | Three firewall profiles | 1 Succeeded; all profiles On, Public active |
| BFL-WIN-Firewall-Test-Block-Edge | Temporary outbound test | Edge blocked, then restored; test rule left Disabled |
| BFL-WIN-BitLocker-Pilot | Encryption requirement and recovery settings | 1 Succeeded; previously encrypted C: remained On |
| BFL-WIN-ASR-Pilot | Six selected ASR rules | Initial Audit deployment succeeded; obfuscated-script rule subsequently tested in Block |
| BFL-WIN-MDE-Onboarding-Pilot | Endpoint detection and response onboarding | 1 Succeeded; Defender device Onboarded and Active |
| BFL-WIN-Tamper-Protection-Pilot | Centrally managed Tamper Protection | 1 Succeeded; endpoint setting On and managed by administrator |

Success reports confirm delivery for the displayed device. They do not prove every configured setting or every protection scenario.

## Defender Antivirus

The owner's configuration summary recorded the following settings:

| Setting | Value |
|---|---|
| Archive scanning, behavior monitoring, cloud protection | Allowed |
| Full-scan removable-drive scanning; downloaded files and attachments | Allowed |
| Real-time monitoring, script scanning, on-access protection | Allowed |
| User UI access | Allowed |
| Cloud block level | High |
| Sample submission | Send safe samples automatically |
| PUA protection | On; detected items blocked |
| Real-time scan direction | All files, bidirectional |
| Retain cleaned malware | 30 days |
| Average CPU load factor | 30 |
| Check signatures before scan; low CPU priority | Enabled |
| Scheduled quick scan time | 720 minutes after midnight |
| Signature update interval | 4 hours |
| Randomize scheduled task times | Scheduled tasks will not be randomized |

The policy makes protection centrally managed, adds PUA blocking and cloud analysis, and schedules lightweight scanning. Scheduling and update cadence were configured but were not observed across a full cycle.

The quick scan completed: **10,852 files, 1 minute 31 seconds, 0 threats**. The owner confirmed real-time and cloud-delivered protection remained On and could not be changed in the UI.

The initial EICAR URL displayed text in the browser without a Protection history entry. That observation alone did not establish antivirus failure. The subsequent test produced “Threat found”; Protection history showed **Threat quarantined**, severity Severe. The entry could not be expanded, so the screenshot does not independently identify its threat name or file path. No real malware was used.

## Firewall and reversible outbound test

The supplied configuration summary enabled Domain, Private and Public firewalls, with default inbound **Block** and outbound **Allow**. Shielded was False and Disable Stealth Mode was False. Local firewall/IPsec policy merging and application/port preference merging were True in that summary.

Logging evidence needs qualification: the copied summary had Domain/Private logging disabled with 1,024 KB files, while Public success/drop logging was enabled with 16,384 KB. Follow-up guidance requested success/drop logging and 16,384 KB for all profiles, with separate pfirewall_Domain.log, pfirewall_Private.log and pfirewall_Public.log files under %systemroot%\system32\LogFiles\Firewall. No final settings export or log contents were supplied. Policy Success is not evidence that every requested logging correction was applied.

The temporary test rule was:

| Field | Value |
|---|---|
| Name | BFL-Test-Block-Edge-Outbound |
| Action / direction | Block / outbound |
| Enabled during test | Enabled |
| File path | C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe |
| Network types | FWPROFILETYPEPUBLIC |
| Description | Temporary pilot test: block Edge outbound traffic on Public profile. |

No port or address restriction was included in the supplied rule summary. With Public active, Bing in Edge failed with **ERR_NETWORK_ACCESS_DENIED**. After disabling the rule and synchronizing, Bing loaded again. The owner also confirmed Outlook Web worked and the test rule's Enabled field was Disabled. The rule was retained in its disabled state.

This test demonstrates the selected application's outbound restriction and recovery; it is not an inbound-network or firewall-log validation.

## BitLocker and recovery-key rotation

C: was already encrypted before this policy. The owner supplied:

| Setting | Value |
|---|---|
| Require Device Encryption | Enabled |
| Configure Recovery Password Rotation | Refresh on for Entra ID-joined devices |
| Choose how operating system drives can be recovered | Enabled |
| Save recovery information to AD DS | True |
| Do not enable BitLocker until recovery information is stored to AD DS | True |
| User recovery information | Require 48-digit recovery password |
| Omit recovery options from setup wizard | True |
| Allow data recovery agent | False |
| 256-bit recovery key | Do not allow |
| AD DS storage selection | Store recovery passwords only |

The AD DS labels above are copied from the policy summary; they are not proof that this Entra-joined device backed a key up to on-premises AD DS. The observed recovery-key entry was in the cloud management portal. Initial encryption and backup behavior on a newly unencrypted device were not tested.

The policy report showed Success and the owner confirmed C: remained BitLocker On. Later, the owner initiated **BitLocker key rotation** from the device actions. The action report showed **Complete**, and the owner confirmed a **new BitLocker Key Id** appeared while C: remained On. No recovery-password value is recorded here.

This validates the manual rotation workflow. Automatic rotation after recovery-password use and boot-time recovery were not tested.

## Attack surface reduction: Audit to Block

| Selected rule | Initial mode | Final recorded mode |
|---|---|---|
| Block abuse of exploited vulnerable signed drivers | Audit | Audit |
| Block all Office applications from creating child processes | Audit | Audit |
| Block executable content from email client and webmail | Audit | Audit |
| Block execution of potentially obfuscated scripts | Audit | **Block** |
| Block JavaScript or VBScript from launching downloaded executable content | Audit | Audit |
| Block Win32 API calls from Office macros | Audit | Audit |

Audit deployment succeeded. Desktop Word and Excel were not installed, so Office-specific behavior was not tested.

The owner ran the downloaded demonstration file TestFile_ScriptObfuscatedContent_5BEB7EFE-FD9A-4556-801D-275E5FFC04CC.js from Downloads. In Audit, a blank Notepad opened and Defender/Operational recorded **1122**, rule **5BEB7EFE-FD9A-4556-801D-275E5FFC04CC**, process **wscript.exe**. After changing only that rule to Block and synchronizing, the repeat test recorded **1121** for the same rule and script.

The events provide a matched audit/block comparison. They do not validate the other five rules or an EDR alert. Cleanup of the downloaded demonstration script was requested but not explicitly confirmed.

## Optional MDE and Tamper Protection

The owner confirmed the existing Microsoft 365 E5 assignment included Microsoft Defender for Endpoint. Intune initially showed connector Unavailable. After the owner enabled **Microsoft Intune connection** in the Defender portal and saved preferences, Intune showed **Available**.

The onboarding profile used **Auto from connector**. Sample Sharing and the deprecated telemetry-frequency setting were left Not configured in the selected setup. The connector is a service integration; the onboarding policy's intended device scope remained the pilot group.

The Intune onboarding report showed Success. The Defender device page showed:

- BFL-WKS02: **Onboarded**, health **Active**, security operations **Full**.
- Join information: **AAD joined**, primary user Anna Finance.
- Windows 11 build 26300.9457.

“No known risks” and zero early recommendations are observations from that page, not a comprehensive security assessment. An EDR detection alert, incident investigation and response action were not exercised.

The separate Tamper Protection policy enabled **Tamper Protection (Device)**. The Windows Security Center customization/hide settings were left Not configured in the selected setup. Intune reported Success, and the endpoint showed **Tamper Protection On**, greyed out with “This setting is managed by your administrator.” No tamper-bypass test was performed.

The existing license was used; no new trial, Azure compute, Log Analytics workspace or Sentinel deployment was part of this session.

## Troubleshooting and lessons

| Symptom | Investigation and resolution | Evidence boundary |
|---|---|---|
| Assignments offered collections, not groups | Windows (ConfigMgr) had been selected; switched to Windows | No confirmed deployment of the abandoned wizard |
| EICAR text displayed without a detection | Continued with a file-based test; quarantine then observed | Collapsed entry does not expose the exact threat identity |
| Quarantine entry would not expand | Recorded the visible quarantine outcome | Cause was not established |
| Sync failed with 0x80190190 / Bad request (400) | Later synchronization and firewall deployment succeeded | Owner could not identify the cause; do not attribute it to firewall settings |
| ASR demonstration sign-in failed with AADSTS700016 | Message said application not found in the tenant; proceeded with the downloaded demonstration file | Authentication failure was not ASR enforcement |
| Blank Notepad appeared during Audit test | Checked Defender event 1122, then repeated in Block and obtained 1121 | Audit behavior was expected; absence of a blocking dialog was not a failure |
| Intune MDE connector Unavailable | Enabled Microsoft Intune connection and waited for propagation | Available plus endpoint onboarding verified the integration path |
| Portal actions differed from earlier navigation | Used the device's grouped actions for key rotation | Action status, not menu wording, provided the completion evidence |

The most useful validation combined the management report, endpoint state and a controlled functional test. Reversible testing restored browser connectivity without disabling the main firewall or Conditional Access.

## Final state, limitations and next step

The owner confirmed Edge and Outlook Web worked, the temporary firewall rule was Disabled, BFL-WKS02 was Compliant and Conditional Access remained On before the optional steps. The later rotation screenshot again shows Compliant; C: remained BitLocker On after rotation. Browser access and CA were not separately re-tested after every optional action.

Remaining evidence gaps: final firewall logging values and log contents; exact expanded EICAR details; scheduled AV/update cadence; PUA behavior; inbound firewall traffic; the other ASR rules; EDR alert/response; BitLocker boot recovery and automatic rotation; explicit ASR test-file cleanup confirmation. These are not marked PASS.

Next: Day 10 Azure RBAC. First inspect the existing subscription, effective role assignments and scope, credit balance and trial expiry. Plan authorized versus denied operations and distinguish management-plane roles from Storage Blob Data Reader data access before creating resources.
