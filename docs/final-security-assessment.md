# Final security assessment — Baltic Finance

**Assessment date:** 2026-10-07  
**Outcome:** the recorded Day 01–15 educational scope is complete. The lab demonstrates selected security controls and response workflows; production readiness is not established.

[Project overview](../README.md) · [Roadmap](../PROJECT-PLAN.md) · [Repository review](../tests/final-review.md) · [Resource and cost plan](resource-retention-plan.md)

## Scope and method

This assessment reviews the daily implementation notes, test records, console excerpts and published evidence. It does not represent a new penetration test, live configuration audit, certification or billing inspection. The owner performed the technical actions. Documentation review did not rerun Azure, Windows, Intune or Entra tests.

The repository contains 143 screenshots across 15 milestones. Evidence is distinguished as a functional result, a configuration observation, supplied console output or an owner confirmation. A PASS applies only to the stated procedure. Missing exports and unperformed tests remain limitations.

The assessed environment comprises a single local AD DS/DNS domain controller, an AD-joined workstation, a separate Entra-joined Intune workstation, group-scoped Cloud Sync, selected Azure resources, Defender for Cloud and an Azure Activity/Sentinel demonstration. BFL-WKS02 is Entra joined, not hybrid joined. Existing tenant services outside the documented exercises were not comprehensively assessed.

## Results supported by the record

| Area | Demonstrated result | Principal evidence and boundary |
|---|---|---|
| AD foundation | Domain/DNS health, scoped GPO application, Finance access allowed for Anna and denied for Sara | [Days 01](../tests/day-01.md) and [02](../tests/day-02.md); one DC, no recovery test |
| Administrative access | Authorized workstation administration, ordinary-user denial, encrypted LAPS backup/rotation and group-change events | [Day 03](../tests/day-03.md); no LAPS recovery logon or gMSA rollover test |
| Hybrid identity | Selected-group synchronization, excluded administrator, controlled scope fault and disabled-state propagation | [Days 04](../tests/day-04.md) and [05](../tests/day-05.md); some results owner-confirmed; session revocation not tested |
| Device management | Entra join, Intune enrollment, Corporate ownership, session lock and SmartScreen enforcement | [Days 06](../tests/day-06.md) and [07](../tests/day-07.md); aggregate baseline success is not proof of every setting |
| Device-based access | Compliant Outlook access, deliberate noncompliance denial, recovery with the intended CA policy succeeding | [Days 08](../tests/day-08.md) and [15](../tests/day-15.md); one pilot device/user and application |
| Endpoint protection | Quarantine after EICAR exercise, firewall block/recovery, ASR audit/block events, MDE onboarding, managed Tamper Protection and manual key rotation | [Day 09](../tests/day-09.md); no EDR response or BitLocker boot-recovery test |
| Azure authorization | Management-plane allow/deny; Blob read allowed, write denied and read revoked | [Day 10](../tests/day-10.md); Contributor role-assignment restriction observed in UI, not API |
| Workload identity | Managed-identity secret read denied → allowed → revoked; separate secret creation denied | [Day 11](../tests/day-11.md); Automation Test pane, no scheduled-job or private-network test |
| Network controls | HTTP 200 → targeted NSG denial → HTTP 200; matching Network Watcher rule evaluation | [Day 12](../tests/day-12.md); one private TCP path, not an Internet-ingress audit |
| Storage posture | Approved-subnet managed-identity read succeeds after workstation IP removal; workstation request denied; recommendation later Completed | [Day 13](../tests/day-13.md); trusted-services exception retained, no Private Endpoint |
| Detection and response | Successful tag write and role grant produce alerts; investigation, manual resolution and access revocation verified | [Days 14](../tests/day-14.md) and [15](../tests/day-15.md); authorized simulations, not actual compromise |

## Capstone outcomes

**Azure RBAC and Sentinel:** Sara started without current assignments at the target resource group. An Active Permanent Reader grant generated a successful AzureActivity operation and a matching alert. Investigation matched the exact assignment ID to Sara, Reader and resource-group scope; Caller was the actor, not the recipient. The owner removed the role and verified subsequent resource-group read denial. Incident ID 5 and its two alerts are resolved, with Benign Positive classification. [Scenario and evidence](day-15.md).

**Device compliance and Conditional Access:** a temporary minimum OS requirement made only the OS-version check noncompliant on BFL-WKS02. Outlook was blocked and the intended compliant-device CA policy recorded Failure. After the rollback workflow, the device returned to Compliant, Outlook opened and that policy recorded Success. The final policy-field export and existing-session revocation timing were not captured. This scenario used Intune and Entra evidence; it did not create a Sentinel endpoint-compliance incident. [Test cases CAP-B01–B05](../tests/day-15.md#test-cases).

The two Sentinel demonstration rules are retained Disabled. Incident ID 3 from Day 14 is also resolved as Benign Positive. These results demonstrate an investigated and closed exercise, not a continuously operating SOC.

## Secure Score interpretation

| Observation | Day 13 baseline | Follow-up supplied 2026-10-07 |
|---|---|---|
| Secure Score | 34% | 77% |
| Active secure score recommendations | 14/32 | 9/32 |
| Displayed attack paths | 0 | 0 |

The increase is **43 percentage points**. The owner attributes most of it to deletion of two older VMs that were no longer needed for licensing reasons. This changed the assessed resource population. It does not prove their ports were secured or their operating systems patched. The deleted older VMs are separate from the Day 12 VMs recorded as deallocated.

The selected Storage recommendation displays Completed. Exact score contributions were not measured, and no separate Azure Policy Compliant export was supplied. Neither 77% nor zero displayed attack paths is an overall security rating or evidence of no remaining risk. [Baseline, remediation and reassessment](day-13.md#reassessment-follow-up).

## Residual risks and production improvements

Priorities below are assessment judgments, not Microsoft severity ratings. P1 means address before relying on the lab design for business operations; P2 means complete before expanding coverage. These recommendations were not implemented during closure.

| ID / priority | Recorded condition and consequence | Recommended next work | Acceptance evidence |
|---|---|---|---|
| R01 / P1 | One DC and one Cloud Sync agent; no backup/restore or failover validation. Recovery capability is unproven. | Define recovery objectives, protected backups and appropriate redundancy; rehearse recovery in isolation. | Successful documented restore and identity-service recovery test |
| R02 / P1 | Devices without a compliance policy remain Compliant; CA testing covers Anna/Outlook only. Assignment gaps can undermine the intended device requirement. | Inventory assignments and exclusions, test emergency access, then stage a change to the no-policy setting and broader coverage. | Assigned/unassigned-device tests, emergency access and regression results |
| R03 / P1 | Azure Activity is the demonstrated Sentinel source; both rules are Disabled, response is manual and MDE response was not tested. Ongoing detection coverage is not established. | Define required telemetry, enable reviewed production detections, map entities and test an approved response workflow. | Ingestion health, benign/negative tests, incident ownership and response evidence |
| R04 / P1 | Current credit balance, exact trial expiry and actual charges are unknown; CSPM was enabled with paid use accepted. | Execute the owner-controlled resource/cost review before the reported Azure credit expiry of 2026-10-13. | Current billing/license record and explicit retain/retire decisions |
| R05 / P2 | One ASR rule is Block; five selected rules remain Audit. BitLocker recovery and broader endpoint responses were not tested. | Review audit results, stage justified blocking changes and exercise recovery/response. | Application compatibility, selected block tests, recovery and response results |
| R06 / P2 | Storage uses selected networks with a trusted-services exception; no Private Endpoint. Day 12 tested one TCP path. | Review exception necessity, network/data authorization boundaries and ingress inventory; evaluate private connectivity against requirements. | Approved design, allowed/denied access tests and complete effective-rule inventory |
| R07 / P2 | Cloud synchronization/password-change timing, disabled-user login/session behavior and privileged-role lifecycle were not comprehensively tested. | Add identity lifecycle and emergency-access tests, scoped privilege reviews and timed elevation where justified. | Positive/negative sign-ins, revocation results and reviewed effective assignments |
| R08 / P2 | Full final policy/rule exports, patch inventory and several control-level checks are missing. Portal reports can conceal incomplete coverage. | Export sanitized configuration baselines, review drift and validate selected controls on the final OS build. | Versioned exports, patch inventory and reproducible regression results |

For R02, Microsoft recommends marking devices without an assigned compliance policy Not compliant when compliance is used with Conditional Access. Review assignment impact and recovery access before changing the lab. [Microsoft Intune compliance policy settings](https://learn.microsoft.com/en-us/intune/device-security/compliance/overview).
