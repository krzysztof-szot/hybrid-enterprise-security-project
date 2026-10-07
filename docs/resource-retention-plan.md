# Resource retention and cost plan

**Prepared:** 2026-10-07  
**Status:** plan only; no infrastructure, licensing or billing changes performed during documentation closure.

[Assessment](final-security-assessment.md) · [Project overview](../README.md)

## Recommended approach

Preserve the repository as the completed portfolio record. Keep a live cloud demonstration only when there is a defined next use and an accepted budget. Otherwise, the owner can retire specifically reviewed exercise resources after preserving the configuration needed to reproduce them.

The owner reported Azure credit expiry on **2026-10-13**. This is not a newly verified billing deadline. The last reported balance was more than EUR 156 on 2026-10-05; it is not a current balance. Exact offer terms and Microsoft 365 trial expiry remain unverified. Check these before deciding what to retain.

## Recorded state and decisions

| Scope | Last recorded state | Retention choice and dependency |
|---|---|---|
| Local BFL-DC01, BFL-WKS01, BFL-WKS02 | Functional AD and endpoint exercises completed; BFL-WKS02 Compliant/access restored | Retain locally for demonstrations if useful. Keep backups and recovery material private; evaluation/license validity still needs review. No restore capability is assumed. |
| Entra / Intune pilot | Cloud Sync, device management and compliant-device CA retained | Preserve settings if continuing the endpoint lab. Review trial expiry, assignments and recovery access before any license or policy change. |
| Day 12 network exercise | vm-bfl-day12-client and vm-bfl-day12-server recorded Stopped (deallocated); network, disks and public IPs not reported deleted | Choose between a budgeted demonstration environment and owner-executed retirement. Inventory each dependency before removing anything. |
| Day 13 Storage test path | Existing bflmi13a7k29 retained; selected networks, snet-client service endpoint and client managed-identity Blob reader | This account predates the exercise. Do not delete it as part of blanket lab cleanup. Removing the client VM or VNet breaks the demonstrated access path; review its role and network rule as part of any approved retirement. |
| Defender for Cloud | CSPM On / Partial with paid use accepted | Review current coverage and charges. Keep paid capabilities only with a continuing need and budget; no plan has been changed here. |
| law-bfl-sentinel / rg-bfl-sentinel-lab | Azure Activity collection retained; both demonstration rules Disabled; incidents resolved | Keep only with a defined retention/demo purpose. Before any retirement, export required evidence and inspect the subscription policy assignment and diagnostic settings feeding the workspace. |
| Other pre-existing resources | Not comprehensively inventoried | Review independently. Shared or unrelated resources are outside automatic cleanup. |

A deallocated VM is not billed for VM instance usage, but disks and networking resources can still incur charges. Inspect associated resources rather than relying only on the VM status. [Microsoft VM states and billing](https://learn.microsoft.com/en-us/azure/virtual-machines/states-billing).

Sentinel costs depend on the applicable ingestion, analysis, retention and related service configuration. A Disabled analytics rule is not evidence that retained services cost nothing. Verify the actual subscription and workspace charges. [Sentinel billing](https://learn.microsoft.com/en-us/azure/sentinel/billing) and [cost monitoring](https://learn.microsoft.com/en-us/azure/sentinel/billing-monitor-costs).

## Owner execution checklist

1. Record the current Azure credit/offer end date, month-to-date cost and cost by resource/service. Check exact M365 trial expiry and any renewal/payment arrangement.
2. Choose a retention deadline and budget for any cloud demonstration. Record which resources must remain and why.
3. Preserve required sanitized exports and evidence privately or in the repository as appropriate. Never publish credentials, tokens, recovery passwords or VM disks.
4. Inventory dependencies before any removal. In particular, the Day 13 read path depends on the Day 12 client identity and subnet; the Sentinel workspace receives data from subscription-level collection configuration.
5. If retirement is chosen, manually remove only the agreed objects and review retained disks, public IPs, role assignments, network rules and collection configuration. Do not infer that deleting a parent resource removed every related object.
6. Recheck the resource inventory and billing after usage data updates. Record actual results and the chosen retained scope.

None of these closure checks is marked completed without fresh owner evidence. No automatic deletion schedule, subscription cancellation or blanket resource-group deletion is authorized by this document.
