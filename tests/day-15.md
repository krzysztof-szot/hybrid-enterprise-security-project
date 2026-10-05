# Day 15 — Capstone test record

Date: 2026-10-05  
Status: both recorded scenarios completed; evidence reviewed from 22 screenshots and supplied session output.

[Implementation](../docs/day-15.md) · [Evidence with screenshots](../evidence/day-15/README.md) · [Observed output](../evidence/day-15/console-excerpts.md)

## Test cases

All actions were performed manually by the owner. PASS refers to the specific observation below, not a new automated execution or a broader security guarantee.

| ID | Preconditions / procedure | Expected result | Actual result | Status | Evidence |
|---|---|---|---|---|---|
| CAP-A01 | Inspect Sara's current assignments and attempt portal access before the grant | No current assignment; access denied | 0 current assignments; initial 403 for subscriptions/read | PASS | [01](../evidence/day-15/01-sara-access-before.png), [02](../evidence/day-15/02-sara-access-denied.png) |
| CAP-A02 | Enable scoped scheduled role-assignment rule | 5-minute schedule, 15-minute window, incident creation | Saved rule Enabled / Informational; threshold >0 and incident creation enabled | PASS — configuration | [03](../evidence/day-15/03-role-assignment-detection-rule.png) |
| CAP-A03 | Grant Reader at the resource group and open workspace as Sara | Read access allowed within the target scope | Active Permanent / This resource assignment; workspace Overview opened in the owner's Sara session | PASS | [04](../evidence/day-15/04-sara-reader-assignment.png), [05](../evidence/day-15/05-sara-reader-access-allowed.png) |
| CAP-A04 | Inspect AzureActivity and related Sentinel alert | Successful grant produces a matching alert | Success at 14:30:01.799 UTC; log and alert share correlation ID | PASS | [06](../evidence/day-15/06-role-assignment-activity-log.png), [07](../evidence/day-15/07-role-assignment-alert.png) |
| CAP-A05 | Query exact assignment resource ID using Azure CLI | Recipient, role and scope identified | bfl.sara.security / Reader / rg-bfl-sentinel-lab; assignment ID matches log target | PASS | [06](../evidence/day-15/06-role-assignment-activity-log.png), [08](../evidence/day-15/08-role-assignment-investigation.png) |
| CAP-A06 | Remove the test assignment; inspect IAM and retest access | No current role and read denied | 0 current assignments; portal code 401 denies resourceGroups/read | PASS | [09](../evidence/day-15/09-sara-access-after-removal.png), [10](../evidence/day-15/10-sara-access-denied-after-removal.png) |
| CAP-A07 | Classify and resolve linked incident | Incident and alerts resolved as authorized benign test | Incident ID 5: Resolved / Benign Positive; two resolved alerts, 0 active | PASS | [11](../evidence/day-15/11-role-assignment-incident-resolved.png) |
| CAP-A08 | Disable the demonstration rule | Saved rule Disabled | Both Day 14 and Day 15 rules Disabled; 0 active rules | PASS — configuration | [12](../evidence/day-15/12-role-assignment-rule-disabled.png) |
| CAP-B01 | Check device and policy baseline; open Outlook as Anna | Compliant endpoint and working application access | BFL-WKS02 Compliant; Firewall, Antivirus, TPM Compliant; Outlook opens | PASS | [13](../evidence/day-15/13-device-compliance-before.png), [14](../evidence/day-15/14-compliance-policy-before.png), [15](../evidence/day-15/15-office365-access-before.png) |
| CAP-B02 | Temporarily set minimum OS to 10.0.26301.0 and synchronize | OS-version requirement alone fails compliance | Saved threshold; immediate marking action; Minimum OS version Not compliant, other three checks Compliant | PASS | [16](../evidence/day-15/16-compliance-test-minimum-os.png), [17](../evidence/day-15/17-device-noncompliant.png) |
| CAP-B03 | Attempt Outlook access while noncompliant; inspect Entra event | Access denied by the compliant-device CA policy | Compliance block page; 15:57:05 UTC One Outlook Web Failure; intended CA policy Failure | PASS | [18](../evidence/day-15/18-office365-access-blocked.png), [19](../evidence/day-15/19-conditional-access-block-details.png) |
| CAP-B04 | Follow rollback workflow and synchronize device | Device returns to Compliant | BFL-WKS02 Compliant with 18:21 local check-in; final OS-policy field not exported | PASS | [20](../evidence/day-15/20-device-compliance-restored.png) |
| CAP-B05 | Retry Outlook; inspect new CA evaluation | Access restored with compliant-device policy still applied | Outlook opens; 16:23:48 UTC sign-in Success and intended CA policy Success | PASS | [21](../evidence/day-15/21-office365-access-restored.png), [22](../evidence/day-15/22-conditional-access-success.png) |

## Interpretation and attribution

- Sara's successful workspace view was captured during her guided test session; that crop does not independently display the signed-in identity.
- The initial 403 was for subscriptions/read; the final portal error 401 explicitly denied resourceGroups/read. These are distinct recorded requests.
- Caller is the administrator performing the write. The CLI result identifies Sara as the role recipient by matching the exact assignment ID.
- The role grant was Active Permanent and was explicitly removed. No eligible-role activation or automatic expiry was tested.
- Incident ID 5 contains two alerts for the same activity time. Overlapping rule windows could explain this; execution-by-execution duplication was not proven.
- The CA scope, On state, compliance-group assignment and zero-day action were owner-confirmed. Screenshots independently verify the saved threshold, immediate action, failed OS check and CA Failure → Success.
- Device Compliant and recovered Outlook/CA success are verified. Clearing the temporary minimum was the instructed rollback; a final configuration export was not supplied.
- The exact incident determination and saved investigation note are not visible in the final screenshot.

## Not tested or not independently evidenced

- Reader write denial, out-of-scope resource access, subscription-wide role coverage or a privileged-role attack.
- PIM eligibility inventory, activation or approval.
- A deletion-event query, automated role removal or a post-disable negative detection test.
- Complete analytics-rule JSON, all compliance-group members, final compliance settings export or sign-in Device ID correlation.
- MDE alerting or endpoint response in this capstone; no Sentinel ingestion/incident for the compliance scenario.
- Existing-session revocation timing, guaranteed detection latency, full app coverage, actual costs or current license expiry.
- Day 13 Defender reassessment or Secure Score improvement.

## Final state

Sara's test access was removed and denial verified. Incident ID 5 and both alerts are resolved; the rule is Disabled. BFL-WKS02 is Compliant, Outlook works and the applicable compliant-device CA control evaluates successfully. Day 13 reassessment and the final security assessment remain separate follow-up work.
