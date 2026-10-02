# Day 07 — Session excerpts and confirmations

This record transcribes supplied results and summarizes owner confirmations. It is not a new automated test run.

## Registry read

The owner ran the shorter read-only command because the VM clipboard was unavailable:

```powershell
reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v InactivityTimeoutSecs
```

The owner explicitly confirmed:

```text
InactivityTimeoutSecs    REG_DWORD    0x12c
```

This is 300 seconds. No registry screenshot was supplied.

## Behavioral observations

- Initial idle test: session locked automatically after five minutes and required PIN or password.
- Edge: a normal page opened; the Microsoft demo phishing page was blocked; continuation was unavailable.
- Following baseline deployment and Windows restart: Anna reached the desktop, normal browsing worked, both SmartScreen policies remained true/OK, and five-minute lock still worked.

## Assignment and baseline review

- Owner reported BFL-Intune-Pilot-Devices under Excluded groups during session-lock troubleshooting. The subsequent correction was followed by successful profile and setting reports.
- A full baseline settings list was pasted for review while the profile was unassigned.
- The list showed machine inactivity and the two modern Edge settings Not configured, Device Guard/HVCI Not configured, LAPS Not configured, and LSA protection enabled without UEFI lock.
- Additional Defender and BitLocker settings in Administrative Templates were identified for removal from this profile's scope. The owner confirmed saving those corrections.
- A final complete settings export and snapshot confirmation were not supplied.

See [implementation](../../docs/day-07.md) for the tailoring decisions and their evidence limits.
