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

## Final configuration and pending assessment

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
