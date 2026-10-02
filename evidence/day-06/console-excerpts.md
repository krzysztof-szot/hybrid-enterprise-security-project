# Day 06 — Session excerpts and confirmations

These are selected transcriptions and summaries of supplied evidence, not newly executed commands. Unnecessary IDs and the tenant suffix are omitted.

## Final published dsregcmd output

Command: `dsregcmd /status`

Selected fields from [screenshot 02](02-entra-join-status.png):

```text
AzureAdJoined     : YES
EnterpriseJoined  : NO
DomainJoined      : NO
Device Name       : BFL-WKS02
TpmProtected      : YES
DeviceAuthStatus  : SUCCESS
```

## Earlier session screenshot — before rename

The owner supplied a full dsregcmd screenshot in the conversation. It is not one of the three published Day 06 images. Selected fields:

```text
Device Name       : DESKTOP-9GDL6S7
TenantName        : Baltic Finance Lab
AzureAdPrt        : YES
```

## Owner confirmations

- Existing Microsoft 365 E5 trial had approximately three weeks remaining.
- MDM settings were saved; Anna belongs to the pilot group and has M365 E5 with Intune assigned.
- Windows first-run sign-in completed and Anna reached the desktop.
- Local rename could not be performed because administrative privileges were unavailable.
- After an additional Windows restart, hostname and Intune both showed BFL-WKS02.

The intermediate session-only Intune screenshot showed Rename device to BFL-WKS02 and Restart as Complete while the overview still used DESKTOP-9GDL6S7. The final published screenshots establish the resolved state. No root cause beyond the observed need for an additional restart is asserted.

[Evidence inventory](README.md)
