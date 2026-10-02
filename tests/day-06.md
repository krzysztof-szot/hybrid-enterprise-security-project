# Day 06 — Test record

**Session:** 2026-10-02. The owner executed the tests; documentation work did not rerun them.

Preconditions: a separate Windows 11 VM, existing Entra tenant, bfl.anna.finance identity, owner-confirmed Microsoft 365 E5 assignment with Intune enabled, and pilot group membership.

| ID | Procedure | Expected result | Recorded actual result | Status / evidence |
|---|---|---|---|---|
| D06-01 | Select pilot group, save automatic enrollment settings and recheck | Some with BFL-Intune-Pilot-Users | Group selected; owner confirmed saving and Anna's membership/license | PASS — [selection screenshot](../evidence/day-06/01-intune-mdm-user-scope.png) plus owner confirmation |
| D06-02 | Complete Windows work/school setup as bfl.anna.finance | Successful Windows session | Owner reached Anna's desktop | PASS — owner confirmation |
| D06-03 | Inspect dsregcmd /status after final restart | Entra joined, not AD joined, valid device authentication | AzureAdJoined=YES; DomainJoined=NO; DeviceAuthStatus=SUCCESS; BFL-WKS02 | PASS — [device output](../evidence/day-06/02-entra-join-status.png) |
| D06-04 | Inspect SSO State in the initial signed-in session | AzureAdPrt=YES | AzureAdPrt=YES in earlier session screenshot | PASS — [transcribed session excerpt](../evidence/day-06/console-excerpts.md); not in final published crop |
| D06-05 | Inspect enrolled device in Intune | Intune management and Corporate ownership | Intune and Corporate displayed | PASS — [overview](../evidence/day-06/03-intune-device-overview.png) |
| D06-06 | Inspect primary user | Project Anna identity | Anna Finance, bfl.anna.finance UPN | PASS — [overview](../evidence/day-06/03-intune-device-overview.png) |
| D06-07 | Request rename in Intune; restart Windows; compare local and portal names | BFL-WKS02 in both | Final device output and overview agree after additional restart | PASS — screenshots 02 and 03; intermediate result below |

## Intermediate results

- Remote Rename and Restart displayed Complete while the old name remained. The additional Windows restart resolved the observed naming issue. The internal processing order was not diagnosed.
- Local rename was unavailable according to the owner. This is not a completed audit of Anna's administrator group membership.
- The scope screenshot shows selection before confirmation. Persistence is owner-confirmed, not established solely by that image.
- Compliant is visible in Intune. No custom compliance-policy effectiveness test is claimed.

## Not tested

Out-of-scope enrollment denial, custom compliance enforcement, Conditional Access allow/deny, local privilege configuration, full hardware inventory and VMware clipboard repair.

[Implementation](../docs/day-06.md) · [Evidence](../evidence/day-06/README.md)
