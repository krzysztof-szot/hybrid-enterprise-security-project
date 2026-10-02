# Day 08 — Device compliance and Conditional Access

**Session:** 2026-10-02. **Result:** recorded pilot scope completed: compliant access allowed, controlled noncompliance blocked, and access restored with Conditional Access still On.

## Objective and context

Connect Intune device compliance to an actual access decision for Anna on BFL-WKS02. The workstation was already Entra joined and Intune managed; Day 07 configuration and security baseline remained in place. The test application was Outlook Web, recorded as **One Outlook Web** in Entra sign-in logs.

Before this stage, the owner found no custom compliance policies. The tenant setting **Mark devices with no compliance policy assigned as** was **Compliant** and was left unchanged to preserve existing exercises. The earlier default compliance badge alone did not demonstrate enforcement.

The owner reviewed existing Conditional Access scope and reported that only CA010-Block-Legacy-Authentication applied to Anna through All users. CA008-Expense Portal - Block Non-Hybrid Devices targeted a separate user, Hybrid Finance, and Expense Portal, with SG-Emergency-Access excluded and Block access selected. Its device filter used Exclude filtered devices; the expression was not supplied. No existing policy was changed for this exercise.

## Recorded configuration

These settings describe the guided session configuration; the evidence package contains effective results rather than a complete policy export.

| Intune setting | Value |
|---|---|
| Policy | BFL-WIN-Compliance-Pilot |
| Platform | Windows 10 and later |
| Assignment | BFL-Intune-Pilot-Devices |
| Firewall | Require |
| Antivirus | Require |
| Trusted Platform Module (TPM) | Require |
| Mark device noncompliant | 0 days |
| Minimum OS version | Initially unset; temporarily raised for the negative test; restored to unset |
| Other compliance requirements | Left Not configured in this exercise |

| Conditional Access setting | Value |
|---|---|
| Policy | CA-BFL-Pilot-Require-Compliant-Device |
| Included user | bfl.anna.finance |
| Explicit excluded account | bg01@<tenant>.onmicrosoft.com |
| Target resources | Office 365 |
| Grant | Grant access; Require device to be marked as compliant |
| Conditions / session controls | Not configured |
| Deployment | Report-only → On |
| Final state | On, owner-confirmed |

The administrative fallback account was identified, but an emergency-account sign-in test was not supplied. The new rule did not add an MFA requirement. Direct user scope kept the experiment limited to Anna.

## Implementation and validation

1. Created the compliance policy and synchronized BFL-WKS02. An early device view showed only Default Device Compliance Policy; assignment and synchronization were checked. The later per-setting report showed Firewall, Antivirus and TPM all **Compliant**. The initial delay's cause was not established.
2. Created the Conditional Access policy in **Report-only**. Anna signed in to Outlook through her normal Edge work profile on BFL-WKS02. Device info showed **Compliant: Yes**, **Managed: Yes**, and **Azure AD joined**. The policy returned **Report-only: Success**.
3. Switched the policy to **On** and repeated sign-in. Outlook opened, and the enforced policy returned **Success** at **10:00:42Z**.
4. Introduced a reversible compliance failure. The recorded installed OS version was **10.0.26300.9457**; the session's temporary minimum was **10.0.26300.9458**, above that version. This was a test threshold, not a recommended update target. The device report showed **Minimum OS version: Not compliant**, while Firewall, Antivirus and TPM remained Compliant.
5. Repeated Outlook sign-in. The browser required a compliant device and denied access. The new CA policy returned **Failure** at **10:23:41Z**.
6. Removed the temporary minimum OS requirement and synchronized again. A subsequent sign-in returned **Success** at **10:36:32Z**. The owner confirmed BFL-WKS02 was **Compliant**, Outlook opened, and the policy remained **On**.

All three sign-in times above are UTC on 2026-10-02. They are observed events, not measurements of compliance propagation time.

## Troubleshooting and evidence interpretation

The synchronization pane briefly showed **Policies: Partial success (3 of 4)** and **Calculating compliance: Non-compliant**. The aggregate policy warning was not independently diagnosed. The per-setting report subsequently isolated the deliberate OS-version noncompliance; it does not explain every synchronization warning.

Report-only success established evaluation before enforcement. The separate enforced success, failure and recovery events demonstrate the access decision. Recovery did not require disabling Conditional Access or removing the device from management.

## Outcome, limitations and lessons

The pilot demonstrated **allow → deny → restore** with the same compliant-device grant control. Intune evaluated device state; Conditional Access used that state during the tested sign-ins.

- Scope is one user, one device and Outlook Web. Other Office 365 resources and existing sessions were not tested.
- The tenant still marks devices without an assigned compliance policy Compliant. This remains a documented gap outside the pilot.
- The evidence does not establish emergency-account access, immediate session revocation, an evaluation SLA, or a complete tenant-wide CA audit.
- The negative sign-in's separate Device info pane and error code were not supplied; no specific error code is claimed.
- Effective compliance results are not full antivirus, firewall or TPM functional tests. Full configuration/assignment exports and a final OS-field screenshot were not supplied.

See the [test record](../tests/day-08.md), [eight reviewed screenshots](../evidence/day-08/README.md), and [session record](../evidence/day-08/console-excerpts.md).

## Next step

Day 09: inspect effective Defender Antivirus, firewall, BitLocker and ASR settings before introducing dedicated endpoint security policies. Account for Day 07 baseline exceptions and avoid overlapping settings. Review available licensing before any optional MDE integration or additional trial.
