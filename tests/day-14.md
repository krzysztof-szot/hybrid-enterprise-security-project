# Day 14 — Test record

Date: 2026-10-05  
Status: recorded functional scope completed. Rule retained Disabled; incident resolved.

[Implementation and KQL](../docs/day-14.md) · [Evidence](../evidence/day-14/README.md) · [Output excerpts](../evidence/day-14/console-excerpts.md)

## Test cases

| ID | Preconditions / procedure | Expected result | Actual result | Status | Evidence |
|---|---|---|---|---|---|
| SIEM-01 | Create a supported-region workspace and add Sentinel | Workspace available for Sentinel | law-bfl-sentinel in rg-bfl-sentinel-lab, North Europe | PASS | [01](../evidence/day-14/01-sentinel-workspace.png) |
| SIEM-02 | Assign the Azure Activity streaming policy at subscription scope and run remediation | Collection deployment completes | DeployIfNotExists, logsEnabled True; remediation Complete, 1 out of 1 | PASS | [02](../evidence/day-14/02-azure-activity-policy-assignment.png), [03](../evidence/day-14/03-azure-activity-remediation.png) |
| SIEM-03 | Query AzureActivity after deployment | Actual ingested records exist | Initial 0 followed by 5 events, first 11:49:58.541 / last 11:54:03.394 UTC | PASS | [04](../evidence/day-14/04-azure-activity-ingestio.png) |
| SIEM-04 | Save a workspace tag change; inspect correlated events and _ResourceId | Successful operation and intended target identified | TAGS/WRITE Start → Success; _ResourceId identifies law-bfl-sentinel | PASS | [05](../evidence/day-14/05-azure-activity-tag-write.png), [06](../evidence/day-14/06-tag-change-detection-query.png) |
| SIEM-05 | Save the scheduled rule with the tested query | Rule Enabled with 5-minute interval, 15-minute lookback, >0 threshold and incident creation | Saved rule shows these settings and one-hour alert grouping | PASS — configuration | [07](../evidence/day-14/07-sentinel-analytics-rule.png) |
| SIEM-06 | After creating the rule, perform another tag write and inspect Logs and alert details | New event produces a matching Sentinel alert | 12:38:03.783 UTC successful event; alert generated 14:45:46 local with matching correlation ID | PASS | [08](../evidence/day-14/08-tag-change-after-rule.png), [09](../evidence/day-14/09-sentinel-incident.png) |
| SIEM-07 | Open the incident linked to the alert | Incident exists with test alert(s) | Incident ID 3, Informational, Active, two grouped alerts | PASS | [10](../evidence/day-14/10-sentinel-incident-details.png) |
| SIEM-08 | Review as authorized test, classify and resolve | Incident and related alerts resolved | Resolved / Benign Positive; both alerts Resolved; Active alerts 0 | PASS | [11](../evidence/day-14/11-sentinel-incident-resolved.png) |
| SIEM-09 | Disable demonstration rule after validation | Rule retained but inactive | Disabled; active-rule count 0 | PASS — configuration | [12](../evidence/day-14/12-sentinel-rule-disabled.png) |

## Troubleshooting observations

- Poland Central workspace could not be onboarded to Sentinel. West Europe rejected new customers in the owner's attempt. North Europe deployment succeeded.
- Remediation initially showed Evaluating / 0 out of 0; it later completed with 1 out of 1.
- An empty result when filtering ResourceGroup did not mean ingestion had failed. Unfiltered queries revealed policy events; _ResourceId subsequently identified the actual workspace tag target.
- The first five records shared a correlation ID and described policy/deployment stages, not five separate user tests.
- Scheduling units initially showed Hours; the final saved screen verifies Minutes.
- Two alerts refer to the same activity time. This is consistent with overlapping 5/15-minute runs, with grouping enabled; exact execution-by-execution attribution was not exported.

## Limits

PASS is limited to the recorded procedures. The test did not establish malicious intent, prevention, actor identity attribution, entity mapping, endpoint response or automated containment. The requested Day14Test values are not visible in the supplied event payload projections. Only the operation, target and correlation were verified.

No unrelated-resource negative test or post-disable tag-write test was performed. No rule JSON or final diagnostic-settings export was supplied. The observed approximately 7-minute-42-second event-to-alert interval is one result, not an SLA. Exact saved determination and resolution note were not visible.

The rule is disabled; collection resources remain. Day 13 Defender reassessment remains unverified.
