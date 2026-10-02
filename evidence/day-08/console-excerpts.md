# Day 08 — Session record and owner confirmations

This file follows the repository's evidence naming convention. It contains a transcription and summary of portal results and owner statements, **not shell output or an automated test run**. Session: 2026-10-02. Tenant suffixes are omitted.

## Starting conditions — owner-reported

- No custom Intune compliance policy initially.
- Mark devices with no compliance policy assigned as: Compliant; left unchanged.
- Only CA010-Block-Legacy-Authentication was reported to apply to Anna before this exercise.
- CA008 targeted the separate Hybrid Finance user and Expense Portal; its Exclude filtered devices expression was not supplied.
- bg01 was identified as administrative fallback. Login was not demonstrated.

## Initial compliance — supplied portal text

```text
BFL-WIN-Compliance-Pilot
Firewall: Compliant
Antivirus: Compliant
Trusted Platform Module (TPM): Compliant
```

## OS inventory — owner-supplied values

```text
Operating system: Windows
Operating system version: 10.0.26300.9457
Operating system language: en-US
Operating system edition: Enterprise
Operating system SKU: Windows 10/11 Enterprise Evaluation (72)
Security patch level: Not available
```

The session used a temporary minimum OS version of 10.0.26300.9458 to induce noncompliance. The published setting-status screenshot shows the failed check rather than the numeric threshold.

## Intermediate synchronization observation

```text
Notifying device: Completed
Policies: Partial success — 3 of 4 succeeded
Applications: Completed — No application updates
Calculating compliance: Non-compliant
```

This was visible in a screenshot supplied in the conversation. The cause of the aggregate partial-success message was not established.

## Sign-in evidence timeline

| UTC on 2026-10-02 | Evidence | Result |
|---|---|---|
| 09:51:18Z | Screenshots 02–03 | Managed and compliant device; pilot Report-only: Success |
| 10:00:42Z | Screenshot 04 | Pilot RequireCompliantDevice Success; overall Success |
| 10:23:41Z | Screenshot 07 | Pilot RequireCompliantDevice Failure; overall Failure |
| 10:36:32Z | Screenshot 08 | Pilot RequireCompliantDevice Success; overall Success |

## Final owner confirmation

Original statement:

> potwierdzam: BFL-WKS02 ma Compliant, Outlook otwiera się, a polityka CA pozostaje On

English: BFL-WKS02 is Compliant, Outlook opens, and the CA policy remains On.

The recovery followed removal of the temporary OS requirement and synchronization. The screenshot confirms the successful CA decision; final device status and policy state are corroborated by the owner, not a complete configuration export.
