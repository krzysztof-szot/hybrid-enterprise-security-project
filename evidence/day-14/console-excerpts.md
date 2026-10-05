# Day 14 — Observed query and portal output

Transcribed from owner-supplied output and screenshots. No commands were executed against Azure as part of this documentation update. Subscription and personal identifiers are omitted.

## Ingestion summary

Source: [04](04-azure-activity-ingestio.png).

```text
Events: 5
FirstEvent [UTC]: 2026-10-05T11:49:58.5414673Z
LastEvent [UTC]: 2026-10-05T11:54:03.394778Z
```

The owner previously received 0. Inspection of the five events showed DEPLOYIFNOTEXISTS/ACTION and DEPLOYMENTS/WRITE with one shared correlation ID.

## Initial tag-write test

Source: [05](05-azure-activity-tag-write.png), [06](06-tag-change-detection-query.png), plus supplied target-query output.

```text
2026-10-05T12:06:58.7968215Z  MICROSOFT.RESOURCES/TAGS/WRITE  Start
2026-10-05T12:06:59.0624565Z  MICROSOFT.RESOURCES/TAGS/WRITE  Success
CorrelationId: d3994cdf-4d8d-46f6-a83d-250ff0e818d8
```

The target query also returned WORKSPACES/WRITE Success at 12:07:19.462 UTC. _ResourceId pointed to law-bfl-sentinel within rg-bfl-sentinel-lab, and Authorization_d.scope ended with /providers/Microsoft.Resources/tags/default. The full subscription path is deliberately not reproduced.

## Post-rule test and alert

Source: [08](08-tag-change-after-rule.png), [09](09-sentinel-incident.png).

```text
TimeGenerated [UTC]: 2026-10-05T12:38:03.7839529Z
OperationNameValue: MICROSOFT.RESOURCES/TAGS/WRITE
ActivityStatusValue: Success
CorrelationId: 29e1e880-73a8-425e-a663-50e89ddb0cc9
Workspace: law-bfl-sentinel
Alert generated: Oct 5, 2026 2:45:46 PM (displayed local time)
Detection source: Scheduled detection
Service source: Microsoft Sentinel
```

## Incident and final state

Sources: [10](10-sentinel-incident-details.png), [11](11-sentinel-incident-resolved.png), [12](12-sentinel-rule-disabled.png).

```text
Incident ID: 3
Name: BFL-Day14-Workspace-Tag-Change
Created: Oct 5, 2026 2:45:47 PM (displayed local time)
Initial captured state: Active; 2 active alerts
Final captured state: Resolved; Benign Positive; 0 active alerts
Both linked alerts: Resolved
Final analytics rule status: Disabled
Active rules: 0
```

The saved determination and resolution-note text were not visible. No entities were mapped, and the incident graph showed No entities to display.
