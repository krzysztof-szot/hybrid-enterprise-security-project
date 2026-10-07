# Day 13 — Evidence inventory

Sixteen owner-supplied screenshots are published. They document Storage network remediation, successful managed-identity reads, a workstation denial, the later Completed recommendation and a Secure Score follow-up supplied on 2026-10-07.

[Implementation](../../docs/day-13.md) · [Tests](../../tests/day-13.md) · [Actual output excerpts](console-excerpts.md)

## 01 — 01-defender-plans.png

Defender CSPM On with Partial coverage; Foundational CSPM Full. Visible workload plans are Off.

![01-defender-plans](01-defender-plans.png)

## 02 — 02-recommendations-before.png

Initial recommendation inventory; risk Not evaluated and governance status Unassigned are visible.

![02-recommendations-before](02-recommendations-before.png)

## 03 — 03-secure-score-before.png

Saved baseline: Secure Score 34%, 14/32 active recommendations, zero displayed attack paths. Not a post-remediation score.

![03-secure-score-before](03-secure-score-before.png)

## 04 — 04-storage-network-before.png

Initial Storage access enabled from all networks.

![04-storage-network-before](04-storage-network-before.png)

## 05 — 05-storage-network-after.png

Intermediate selected-networks configuration: one IPv4 allow rule, one exception, no VNet yet. Superseded by 13.

![05-storage-network-after](05-storage-network-after.png)

## 06 — 06-storage-access-allowed.png

Workstation Storage browser lists the existing 205-byte blob using Access key authentication.

![06-storage-access-allowed](06-storage-access-allowed.png)

## 07 — 07-storage-recommendation-details.png

Medium-severity VNet-rules recommendation and remediation instructions; one affected account.

![07-storage-recommendation-details](07-storage-recommendation-details.png)

## 08 — 08-storage-vnet-rule.png

vnet-bfl-day12 / snet-client allowed; service endpoint status Enabled.

![08-storage-vnet-rule](08-storage-vnet-rule.png)

## 09 — 09-storage-blob-reader-assignment.png

Client VM managed identity has Storage Blob Data Reader at identity-lab container scope (This resource).

![09-storage-blob-reader-assignment](09-storage-blob-reader-assignment.png)

## 10 — 10-storage-vm-access-allowed.png

VM token acquisition and Blob GET: HTTP 200, 205 bytes, PASS before workstation IP-rule removal.

![10-storage-vm-access-allowed](10-storage-vm-access-allowed.png)

## 11 — 11-storage-vm-access-after-ip-removal.png

Repeated VM GET after IP-rule removal: HTTP 200, 205 bytes, PASS.

![11-storage-vm-access-after-ip-removal](11-storage-vm-access-after-ip-removal.png)

## 12 — 12-storage-public-access-blocked.png

Workstation portal container request returns 403. Networking is identified as a possible cause; authentication selector is not shown.

![12-storage-public-access-blocked](12-storage-public-access-blocked.png)

## 13 — 13-storage-network-final.png

Final summary: selected networks, one VNet, no IPv4 allow rules and one retained exception.

![13-storage-network-final](13-storage-network-final.png)

## 14 — 14-storage-recommendation-pending.png

Historical state immediately after functional tests: the account is still listed and reassessment is pending at that point. The later result is recorded in screenshot 15.

![14-storage-recommendation-pending](14-storage-recommendation-pending.png)

## 15 — 15-storage-recommendation-completed.png

The selected recommendation shows bflmi13a7k29 with Status Completed. The top affected-resource counter is 0; the filtered table includes the completed account. Risk level Not evaluated is a separate field. This verifies the displayed Defender status, not a separate Azure Policy Compliant assessment.

![15-storage-recommendation-completed](15-storage-recommendation-completed.png)

## 16 — 16-secure-score-after-reassessment.png

Follow-up supplied on 2026-10-07: Secure Score 77%, 9/32 active recommendations and 0 displayed attack paths. Resource health shows Unhealthy 4, Healthy 2 and Not applicable 5. Compared with screenshot 03, the score is up 43 percentage points and active recommendations are down from 14/32 to 9/32. The owner attributes the increase mainly to deleting two older VMs for licensing reasons. No exact contribution from individual changes was measured.

![16-secure-score-after-reassessment](16-secure-score-after-reassessment.png)

## Reading the sequence

Screenshots 05–06 capture an intermediate IP-allowlisted state. Screenshot 08 adds the subnet rule, 09 establishes the container role assignment, 10–11 show actual VM reads, and 12–13 show the workstation denial and final network configuration. Screenshot 14 preserves the initially pending assessment; screenshot 15 records the later Completed status.

The 34% screenshot is the baseline and screenshot 16 records 77% after reassessment. The changed resource population prevents attributing the entire increase to Storage remediation. Deletion of the two older VMs is owner-confirmed, not independently proven by this score screenshot. No private-link deployment or write-denial test is evidenced. Client deallocation is also an owner confirmation.

Public endpoint network restriction does not imply anonymous data access was enabled before the change, or that the public endpoint is now disabled. A trusted-services exception remains.

Keep tokens, keys, SAS URLs, unnecessary identifiers and personal information out of future evidence.
