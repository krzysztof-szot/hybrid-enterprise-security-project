# Day 06 — Entra join and Intune enrollment

**Session:** 2026-10-02  
**Status:** Recorded scope completed.

## Objective and design

Enroll a separate Windows 11 workstation into Microsoft Entra ID and Intune, verify device identity, ownership and primary user, and establish the project device name.

BFL-WKS02 was prepared as a new VMware VM. BFL-WKS01 remains the AD-joined workstation for earlier GPO, LAPS and service-account exercises. BFL-WKS02 is **Microsoft Entra joined**, not AD domain joined or hybrid joined. Anna's identity is synchronized from AD; that does not make her device hybrid joined.

The owner reported an existing Microsoft 365 E5 trial with approximately three weeks remaining and confirmed assignment to bfl.anna.finance with Intune enabled. The exact expiration date was not recorded. No new trial or Azure VM was required.

The host reported 120.5 GB free on a 447.5 GB C: volume before installation. The supplied VM setup instructions specified Windows 11 Enterprise Evaluation, 4 GB RAM, two vCPUs, a dynamically allocated 64 GB disk, VMnet8 NAT, UEFI, Secure Boot and virtual TPM 2.0. These are the setup parameters, not an independently collected hardware inventory. The final output directly confirms TpmProtected=YES.

## Enrollment scope

A cloud security group, **BFL-Intune-Pilot-Users**, was created for the pilot with assigned membership. The owner confirmed Anna's membership.

The previous MDM user scope was None. It was changed to:

| Setting | Recorded value |
|---|---|
| MDM user scope | Some |
| Selected group | BFL-Intune-Pilot-Users |
| MDM URLs | Existing default values retained |
| Disable MDM enrollment when adding work or school account on Windows | No |
| Windows Information Protection user scope | None |

The [scope screenshot](../evidence/day-06/01-intune-mdm-user-scope.png) shows the selected group in the open selection panel. Saving was subsequently explicitly confirmed by the owner; an additional session screenshot showed Some with one selected group. The published selection screenshot alone is not proof of persistence.

## Windows setup and sign-in

The owner completed Windows first-run setup using the work/school account `bfl.anna.finance@<tenant>.onmicrosoft.com` and reached Anna's desktop successfully. The tenant suffix is masked in public documentation.

The device appeared in Intune with management by Intune, Corporate ownership and Anna Finance as primary user. This establishes enrollment separately from successful Windows sign-in.

## Verification

The [final device output](../evidence/day-06/02-entra-join-status.png) records:

```text
Device Name       : BFL-WKS02
AzureAdJoined     : YES
DomainJoined      : NO
TpmProtected      : YES
DeviceAuthStatus  : SUCCESS
```

An earlier session screenshot also showed TenantName=Baltic Finance Lab and AzureAdPrt=YES. Its SSO section is not included in the final published crop; this distinction is retained in the [session excerpts](../evidence/day-06/console-excerpts.md).

The [Intune overview](../evidence/day-06/03-intune-device-overview.png) independently shows BFL-WKS02, Corporate ownership, Intune management and Anna Finance as primary user.

## Troubleshooting — device name

Windows initially used **DESKTOP-9GDL6S7**. Naming the VM in VMware did not set the Windows hostname.

The owner reported being unable to rename Windows locally because administrative privileges were unavailable. No local-group membership or token inspection was supplied, so the underlying privilege configuration is not established by this report.

The administrator submitted **Rename device to BFL-WKS02**, with restart, through Intune. The action-status screen showed both Rename and Restart as Complete, while the old name remained visible in Intune and Windows. The owner then performed an additional restart from inside Windows and confirmed that both hostname and the Intune device name became BFL-WKS02.

**Observed resolution:** an additional Windows restart was followed by the expected name in both places. The precise timing of rename processing versus the first restart was not established; Complete alone did not prove that the visible hostname had changed. No re-enrollment or new administrator assignment was needed.

## Results and limitations

The planned device join, enrollment, ownership, primary-user and naming checks passed. Intune also displayed **Compliant**, but no new project compliance policy or non-compliant negative test was implemented in this session. That badge is an observed portal state, not proof of Day 08 policy enforcement.

Other limits: exact trial expiry and full VM hardware inventory were not captured; local administrator membership was not audited; out-of-scope enrollment denial and Conditional Access were not tested. Missing VMware clipboard integration was worked around with host screenshots and was not repaired.

## Next stage

Day 07: inspect existing Intune assignments, then introduce justified pilot configuration profiles and verify endpoint results. Retain BFL-WKS01 for the earlier AD exercises. Dedicated compliance and Conditional Access testing remains Day 08.

[Test record](../tests/day-06.md) · [Evidence inventory](../evidence/day-06/README.md)
