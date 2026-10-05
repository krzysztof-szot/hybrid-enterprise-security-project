# Day 15 — Commands and observed results

Session: 2026-10-05. These are transcribed excerpts and queries from the owner's manual exercise, not new executions. Tenant domains, subscription IDs and actor details are omitted or replaced with placeholders.

[Implementation](../../docs/day-15.md) · [Tests](../../tests/day-15.md) · [Screenshots](README.md)

## Interactive role-assignment verification

Source: [06](06-role-assignment-activity-log.png).

```kusto
AzureActivity
| where TimeGenerated > ago(1h)
| where OperationNameValue =~ "Microsoft.Authorization/roleAssignments/write"
| where ActivityStatusValue in~ ("Success", "Succeeded")
| extend TargetResource = coalesce(
    _ResourceId,
    ResourceId,
    tostring(Authorization_d.scope))
| where TargetResource contains "/resourcegroups/rg-bfl-sentinel-lab/"
    or TargetResource endswith "/resourcegroups/rg-bfl-sentinel-lab"
| project TimeGenerated, OperationNameValue,
          ActivityStatusValue, TargetResource,
          Caller, CorrelationId
| order by TimeGenerated desc
```

```text
TimeGenerated [UTC]: 2026-10-05T14:30:01.7997622Z
OperationNameValue: MICROSOFT.AUTHORIZATION/ROLEASSIGNMENTS/WRITE
ActivityStatusValue: Success
TargetResource: /subscriptions/<redacted>/resourcegroups/rg-bfl-sentinel-lab/providers/microsoft.authorization/roleassignments/5522728c-1a98-429b-b2b6-5b72cdc38b60
Caller: <redacted administrator>
CorrelationId: fd08d57f-09e3-4f3a-bee9-8c2cfba55f65
```

## Payload investigation

The following query was run during the session. The supplied expanded result contained no requestbody with the recipient or role, so investigation continued through the exact role assignment. The raw screenshot included personal data and is not part of the numbered evidence inventory.

```kusto
AzureActivity
| where TimeGenerated > ago(24h)
| where CorrelationId == "fd08d57f-09e3-4f3a-bee9-8c2cfba55f65"
| where OperationNameValue =~ "Microsoft.Authorization/roleAssignments/write"
| where ActivityStatusValue in~ ("Success", "Succeeded")
| project TimeGenerated, Properties_d, Authorization_d
```

## Identify the exact assignment

Source: [08](08-role-assignment-investigation.png). Read-only command; the GUID belongs to this historical test.

```bash
az role assignment list --resource-group rg-bfl-sentinel-lab --query "[?name=='5522728c-1a98-429b-b2b6-5b72cdc38b60'].{Assignment:name,Recipient:principalName,Role:roleDefinitionName,Scope:scope}" --output json
```

Sanitized observed output:

```json
[
  {
    "Assignment": "5522728c-1a98-429b-b2b6-5b72cdc38b60",
    "Recipient": "bfl.sara.security@<tenant>.onmicrosoft.com",
    "Role": "Reader",
    "Scope": "/subscriptions/<redacted>/resourceGroups/rg-bfl-sentinel-lab"
  }
]
```

The assignment was later removed. Re-running this historical query after removal is not expected to reproduce the original object.

## Access and incident outcomes

Sources: [02](02-sara-access-denied.png), [09](09-sara-access-after-removal.png), [10](10-sara-access-denied-after-removal.png), [11](11-role-assignment-incident-resolved.png), [12](12-role-assignment-rule-disabled.png).

```text
Before grant: HTTP 403 / AuthorizationFailed
Denied action: Microsoft.Resources/subscriptions/read

After removal: Current role assignments = 0
Portal error code: 401
Denied action: Microsoft.Resources/subscriptions/resourceGroups/read
Resource group: rg-bfl-sentinel-lab

Incident: ID 5 / BFL-Day15-Role-Assignment-Added
Created: 2026-10-05 16:43:10 local
Final status: Resolved
Final displayed classification: Benign Positive
Linked alerts: 2; both Resolved
Active alerts: 0
Day 15 rule: Disabled
Day 14 rule: Disabled
Active rules: 0
```

## Device compliance and application access

Sources: [16](16-compliance-test-minimum-os.png), [17](17-device-noncompliant.png), [19](19-conditional-access-block-details.png), [20](20-device-compliance-restored.png), [22](22-conditional-access-success.png). Installed build came from the separate winver screenshot supplied in the session.

```text
Device: BFL-WKS02
Installed OS: Windows 11 26H2 / build 26300.9457
Initial Minimum OS version: Not configured (supplied editor text)
Test Minimum OS version: 10.0.26301.0
Mark device noncompliant: Immediately

Policy evaluation during test:
  Minimum OS version: Not compliant
  Firewall: Compliant
  Antivirus: Compliant
  Trusted Platform Module (TPM): Compliant

2026-10-05T15:57:05Z:
  Application: One Outlook Web
  Sign-in status: Failure
  CA-BFL-Pilot-Require-Compliant-Device: Failure
  Grant control: RequireCompliantDevice

Recovery:
  BFL-WKS02: Compliant
  Last check-in shown: 2026-10-05 18:21 local

2026-10-05T16:23:48Z:
  Application: One Outlook Web
  Sign-in status: Success
  CA-BFL-Pilot-Require-Compliant-Device: Success
  Grant control: RequireCompliantDevice
```

The final Minimum OS version field, Entra numeric error code and sign-in Device info were not exported. The restored application access and successful CA evaluation are directly shown.
