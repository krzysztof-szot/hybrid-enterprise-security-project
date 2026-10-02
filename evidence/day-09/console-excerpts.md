# Day 09 — Selected session and event excerpts

**Date:** 2026-10-02. These are transcriptions of visible screenshot fields and the owner's confirmations, not newly executed console commands. No raw EVTX, firewall log or full configuration export was supplied.

[Evidence inventory](README.md) · [Implementation](../../docs/day-09.md) · [Tests](../../tests/day-09.md)

## Quick scan and quarantine

Screenshot 02: quick scan completed; 10,852 files scanned; duration 1 minute 31 seconds; 0 threats found; security intelligence up to date.

Screenshot 03: “Threat quarantined”, severity “Severe”, following the EICAR exercise. The owner reported a threat notification but could not expand the history entry. Exact threat name and path therefore remain unverified.

The owner's separate confirmation: real-time and cloud-delivered protection were On and could not be changed.

## ASR events

Source: Microsoft-Windows-Windows Defender/Operational on BFL-WKS02.

| Field | Audit screenshot 10 | Block screenshot 11 |
|---|---|---|
| Event ID | 1122 | 1121 |
| Level | Information | Warning |
| Detection time embedded in event | 2026-10-02T16:11:08.741Z | 2026-10-02T16:18:55.116Z |
| Rule ID | 5BEB7EFE-FD9A-4556-801D-275E5FFC04CC | Same |
| Process | C:\Windows\System32\wscript.exe | Same |
| Script basename | TestFile_ScriptObfuscatedContent_5BEB7EFE-FD9A-4556-801D-275E5FFC04CC.js | Same |
| Location | User's Downloads folder | Same |
| Meaning shown by event | Operation audited | Operation blocked |

Both filtered views show two matching events; the table transcribes the selected event only. The owner observed a blank Notepad during Audit. The second run produced the matching Block event after the rule mode changed.

Guest UI display times differ from portal display times. The UTC detection timestamps above come from the event text; no clock fault is inferred solely from displayed timezone differences.

## Firewall recovery and regression

The owner confirmed:

- Domain, Private and Public firewalls On; Public active.
- Edge and Outlook Web worked after the temporary block was removed by disabling its rule.
- BFL-Test-Block-Edge-Outbound: Enabled = Disabled.
- BFL-WKS02: Compliant; Conditional Access policy remained On.

The negative screenshot shows ERR_NETWORK_ACCESS_DENIED; the recovery screenshot shows Bing loaded. These confirmations preceded the optional MDE/Tamper/rotation steps.

## MDE and Tamper Protection

Owner reported Microsoft Defender for Endpoint enabled in Anna's existing Microsoft 365 E5 license.

Connector progression: Unavailable → enable Microsoft Intune connection → preferences saved → Available.

Screenshot 13: Onboarding status Onboarded; Health state Active; Security operations Full; Domain AAD joined. This establishes onboarding/health, not a completed EDR alert exercise.

Screenshot 15: Tamper Protection On with the administrator-management message and greyed control.

## BitLocker rotation

Screenshot 16:

- Action: BitLocker key rotation.
- Request status: Complete.
- Requested: 10/2/2026, 7:18:02 PM.
- Last updated: 10/2/2026, 7:18:25 PM.
- Device badge: Compliant.

Times above are reproduced as displayed; a timezone is not assigned. The owner separately confirmed a new BitLocker Key Id appeared and C: still had BitLocker On. No recovery password or key identifier value is transcribed.

## Troubleshooting observations

- Sync attempt: 0x80190190, Bad request (400). A subsequent attempt succeeded; root cause unknown.
- Demonstration authentication: AADSTS700016, application not found in the tenant. This did not establish ASR failure.
- Initial policy wizard offered ConfigMgr collections; switched to the Windows platform for Intune group assignment.
- The published antivirus screenshot 01 shows 1 Succeeded. An earlier conversation capture had zero summary counters despite a Success row; the published image is the evidence used in this record.
