# Day 13 — Test record

Date: 2026-10-05  
Scope: `bflmi13a7k29 / identity-lab`, the Day 12 client subnet and VM.  
Status: recorded functional checks PASS; Defender reassessment PENDING.

[Implementation and test script](../docs/day-13.md) · [Evidence](../evidence/day-13/README.md) · [Output excerpts](../evidence/day-13/console-excerpts.md)

## Checks and results

| ID | Preconditions and procedure | Expected result | Actual result | Status | Evidence |
|---|---|---|---|---|---|
| CSPM-01 | Inspect Defender plans, recommendations and classic Secure Score before remediation | Capture a baseline and one actionable finding | Defender CSPM On / Partial; saved score 34%; VNet-rules finding for one Storage account | PASS — baseline recorded | [01](../evidence/day-13/01-defender-plans.png), [02](../evidence/day-13/02-recommendations-before.png), [03](../evidence/day-13/03-secure-score-before.png), [07](../evidence/day-13/07-storage-recommendation-details.png) |
| STOR-01 | Selected networks with temporary workstation IP rule; open identity-lab using Access key in Storage browser | Existing blob can be listed from the allowed workstation | bfl-identity-proof.txt listed, size 205 bytes | PASS — listing only | [05](../evidence/day-13/05-storage-network-after.png), [06](../evidence/day-13/06-storage-access-allowed.png) |
| STOR-02 | Client running in snet-client; service endpoint and subnet rule enabled; managed identity assigned Blob reader at container scope; execute documented GET script | Authenticated read succeeds without a Storage key in the script | Token acquired, HTTP 200, 205 bytes, explicit PASS | PASS | [08](../evidence/day-13/08-storage-vnet-rule.png), [09](../evidence/day-13/09-storage-blob-reader-assignment.png), [10](../evidence/day-13/10-storage-vm-access-allowed.png) |
| STOR-03 | Remove workstation IP allow rule and save; repeat the same VM script | VM read remains allowed | Token acquired, HTTP 200, 205 bytes, explicit PASS | PASS | [11](../evidence/day-13/11-storage-vm-access-after-ip-removal.png), [13](../evidence/day-13/13-storage-network-final.png) |
| STOR-04 | After IP removal, retest the container view from the workstation | Workstation data access is denied | HTTP 403; portal message identifies Storage networking as a possible blocker | PASS — observed workstation denial | [12](../evidence/day-13/12-storage-public-access-blocked.png) |
| STOR-05 | Inspect final Storage Networking summary | Selected networks, approved VNet, no public IPv4 allow rule | Selected networks; Virtual networks 1; IPv4 addresses None; Exceptions 1 | PASS — configuration check | [13](../evidence/day-13/13-storage-network-final.png) |
| CSPM-02 | Revisit the recommendation after remediation | A later assessment confirms the resource no longer violates the selected recommendation | Resource still listed; risk Not evaluated, governance status Unassigned; freshness interval 30 Min | PENDING | [14](../evidence/day-13/14-storage-recommendation-pending.png) |
| CSPM-03 | Compare an assessed post-change Secure Score against the saved baseline | Document an actual post-change score without unsupported attribution | No post-remediation score supplied | NOT VERIFIED | Baseline only: [03](../evidence/day-13/03-secure-score-before.png) |

## Evidence boundaries

- Tests were performed by the owner. Documentation updates do not constitute new Azure test executions.
- Screenshot 06 proves a listing using Access key, not a file download or an Entra-authenticated read.
- Screenshot 12 proves a portal 403. It does not display the authentication method; retaining the baseline method was instructed. The final network settings and VM success support the network-control interpretation.
- VM output verifies a 205-byte read without exposing the body. Content integrity, write denial and role revocation were not tested.
- One trusted-services exception remains. No Private Endpoint was created and public network access is not globally Disabled.
- Risk level Not evaluated and governance status Unassigned must not be used as a substitute for compliance/health evidence.
- The 34% baseline is not an after-remediation result. An earlier 32% observation is not a demonstrated improvement caused by this exercise.
- After testing, the owner confirmed client Stopped (deallocated). No power-state screenshot or resource deletion is claimed.

## Outstanding verification

Capture the selected recommendation's post-reassessment health/compliance result and a fresh Secure Score view. If the finding remains, investigate the assessment rather than converting PENDING to PASS from elapsed time alone.
