# Day 07 — Intune configuration and tailored Windows baseline

**Session:** 2026-10-02  
**Status:** Recorded pilot scope completed; individual baseline controls were not all functionally tested.

## Objective and scope

Apply justified device configuration to BFL-WKS02, verify endpoint behavior, and deploy a reviewed Windows security baseline without duplicating the two dedicated profiles.

The owner reported empty device-configuration and Windows configuration-profile lists. The baseline catalog contained templates, but the Windows baseline Profiles list was empty. The owner created the assigned security group **BFL-Intune-Pilot-Devices**, containing BFL-WKS02. This device group is distinct from BFL-Intune-Pilot-Users, which controls automatic enrollment.

All changes targeted the device pilot. BFL-WKS01 remains the AD-joined workstation for previous exercises.

## Profiles and rationale

| Profile | Configuration | Purpose |
|---|---|---|
| BFL-WIN-Session-Lock | Settings catalog; Interactive Logon Machine Inactivity Limit = 300 seconds | Limit exposure of an unattended session |
| BFL-WIN-Edge-SmartScreen | Configure Microsoft Defender SmartScreen = Enabled; Prevent bypassing Microsoft Defender SmartScreen prompts for sites = Enabled | Enforce browser reputation protection and prevent bypass of unsafe-site warnings |
| BFL-WIN-Security-Baseline | Windows 10 and later security baseline, version 25H2, tailored as described below | Apply broader pilot hardening with documented exceptions |

Final intended assignment for each profile: **Included groups: BFL-Intune-Pilot-Devices; Excluded groups: empty**. Successful device processing is supported by the published evidence; a final assignment export was not captured.

## Session-lock troubleshooting

**Problem:** Intune Sync reported Completed, but the profile report had zero records.

**Investigation:** The owner found BFL-Intune-Pilot-Devices under Excluded groups.

**Resolution:** Remove that exclusion and include the group, save the assignment, then synchronize BFL-WKS02.

**Verification:** The device report changed to Success, and the setting report to Succeeded. The owner confirmed this registry result:

```text
InactivityTimeoutSecs    REG_DWORD    0x12c
```

0x12c represents 300 seconds. Without manually locking Windows, the owner observed automatic lock after **5 minutes**, requiring PIN or password. The registry result and timed observation are text confirmations; screenshots 01 and 02 show Intune reports, not the registry.

## Edge SmartScreen verification

In Edge on BFL-WKS02, edge://policy showed SmartScreenEnabled=true and PreventSmartScreenPromptOverride=true, both with Source=Platform, Applies To=Device, Level=Mandatory and Status=OK.

The owner confirmed that a normal website opened. The official Microsoft demonstration URL, `https://demo.smartscreen.msft.net/phishingdemo.html`, produced a SmartScreen warning. With details expanded, the screen stated that the organization blocked continuing to the site; no bypass was available.

This tests browser URL protection. It does not establish Defender for Endpoint onboarding, Network Protection, download protection or detection coverage against all threats.

## Baseline review and exceptions

The profile was first saved **without assignments**. Because the portal did not provide the expected configuration export, the owner supplied a full copied settings list for review. The following scope decisions were made:

| Area | Recorded decision | Reason / evidence boundary |
|---|---|---|
| Machine inactivity limit | Not configured in baseline | Dedicated 300-second profile owns the setting |
| Microsoft Edge: the two SmartScreen settings above | Not configured in baseline | Dedicated Edge profile owns these policies |
| Device Lock password settings | Not configured | Additional device-password policy outside this stage |
| LAPS Backup Directory | Not configured | Entra-backed LAPS for WKS02 was not prepared; Day 03 AD-backed LAPS applies to WKS01 |
| Device Guard and Hypervisor Enforced Code Integrity | Not configured | VM VBS prerequisites not verified; avoid enabling UEFI-locked controls in this pilot |
| Configure Lsa Protected Process | Enabled without UEFI lock | Include LSA protection while keeping policy-based reversibility; runtime protection not separately verified |
| Defender and Firewall sections | Requested all settings Not configured | Dedicated antivirus, ASR, firewall and related tests reserved for Day 09 |
| Administrative Templates: BitLocker fixed/removable drive write restrictions | Not configured after final review | Keep BitLocker configuration in Day 09 |
| Administrative Templates: Defender Block at First Sight, process scanning and routine remediation settings | Not configured after final review | These additional antivirus settings were found outside the main Defender section |
| Windows Hello facial anti-spoofing | true retained | Biometric hardening retained; no biometric test on the VM |
| User Rights and UAC | Reviewed baseline values retained | Restrict privileged operations; standard-user elevation configured to be automatically denied |

The copied list confirmed the main exclusions and LSA choice. The final five Administrative Templates corrections were subsequently confirmed saved by the owner; no complete post-save export was collected. The copied list only exposed the three Firewall enable settings after they were set to Not configured, so hidden dependent values were not independently audited.

Other retained settings in the reviewed list include auditing and PowerShell script-block logging, SMB hardening, AutoRun restrictions, Windows shell SmartScreen, enhanced phishing protection, and legacy Browser/Internet Explorer settings. These are distinct from the two modern Edge policies. This is a **tailored baseline**, not a claim of unmodified Microsoft baseline compliance.

## Deployment and post-restart tests

The owner was instructed to take an offline VMware snapshot named Before-Day07-Security-Baseline before assignment. Snapshot creation was not separately confirmed, and restore was not tested.

The baseline was then assigned to the pilot. Its report shows **one successful device, zero errors and zero conflicts**. After a Windows restart, the owner confirmed:

- Anna could sign in and reach the desktop.
- Edge opened a normal website.
- Both dedicated SmartScreen policies remained true with status OK.
- Automatic session lock still occurred after five minutes.

The unsafe-site test was performed before baseline deployment; only the policy-state and normal-browsing checks were repeated after it.

## Limitations and next stage

The aggregate Succeeded report and selected regression checks do not prove every baseline control is effective. LSA runtime status, individual audit events, biometric protection, User Rights enforcement, snapshot recovery, and out-of-scope device behavior were not separately tested. Not configured is a scope decision, not evidence that a Windows protection is disabled.

Day 08 will introduce and test device compliance and Conditional Access. Dedicated Day 09 antivirus, firewall, ASR and BitLocker work must inspect effective settings and avoid overlap with retained baseline controls.

[Test record](../tests/day-07.md) · [Evidence inventory](../evidence/day-07/README.md) · [Session excerpts](../evidence/day-07/console-excerpts.md)
