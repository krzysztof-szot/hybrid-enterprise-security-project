# Day 13 — Actual output excerpts

Transcribed from the owner's supplied screenshots. These are not new executions.

## VM read with the workstation IP rule still present

Source: [10-storage-vm-access-allowed.png](10-storage-vm-access-allowed.png)

```text
Managed identity token: acquired
HTTP status: 200
Bytes read: 205
PASS: blob read using VM managed identity.
```

## VM read after removing the workstation IP rule

Source: [11-storage-vm-access-after-ip-removal.png](11-storage-vm-access-after-ip-removal.png)

```text
Managed identity token: acquired
HTTP status: 200
Bytes read: 205
PASS: blob read using VM managed identity.
```

## Workstation portal denial

Source: [12-storage-public-access-blocked.png](12-storage-public-access-blocked.png)

```text
This request is not authorized to perform this operation.
Error code
403
```

The portal additionally identifies Storage networking as a possible blocker. Public IP, subscription/resource identifiers and diagnostic identifiers are not transcribed here. The screenshot does not display the authentication selector.

## Final configuration and initial pending assessment

[13-storage-network-final.png](13-storage-network-final.png) shows:

```text
Public network access: Enabled from selected networks
Virtual networks: 1
IPv4 addresses: None
Exceptions: 1
```

[14-storage-recommendation-pending.png](14-storage-recommendation-pending.png) still lists the account under the selected recommendation, with a 30 Min freshness interval, risk level Not evaluated and status Unassigned. This is not a successful reassessment result.

## Owner confirmation

After the tests, the owner reported that vm-bfl-day12-client was Stopped (deallocated). This statement is not a captured Azure CLI or portal output.

No token, Storage key, SAS or blob contents are included.

## Reassessment follow-up

Sources: [15](15-storage-recommendation-completed.png), [16](16-secure-score-after-reassessment.png). Score follow-up supplied on 2026-10-07.

```text
Storage account: bflmi13a7k29
Recommendation: Storage accounts should restrict network access using virtual network rules
Displayed status: Completed
Top affected-resource counter: 0
Risk level: Not evaluated

Secure Score: 77%
Active secure score recommendations: 9/32
Displayed attack paths: 0
Resource health: Unhealthy 4 / Healthy 2 / Not applicable 5
```

The saved baseline was 34% and 14/32 active recommendations; the score difference is +43 percentage points. The owner attributes the increase mainly to deletion of two older VMs no longer needed for licensing reasons. No exact score attribution, separate Azure Policy Compliant result or VM deletion-log export was supplied. Screenshot 14 remains a historical pending result, superseded by 15 for the displayed recommendation status.
