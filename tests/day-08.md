# Day 08 — Test record

**Date:** 2026-10-02. **Scope:** Anna / BFL-WKS02 / Outlook Web. Tests were performed by the owner; this record documents supplied evidence, not new automated runs.

Preconditions: BFL-WKS02 Entra joined and Intune managed; Anna using her normal Edge work profile; BFL-WIN-Compliance-Pilot delivered; CA-BFL-Pilot-Require-Compliant-Device scoped to the pilot. See [configuration](../docs/day-08.md).

| ID | Preconditions and procedure | Expected result | Actual result | Status / evidence |
|---|---|---|---|---|
| D08-01 | Synchronize device with Firewall, Antivirus and TPM required; inspect per-setting compliance | All three checks compliant | All three Compliant | PASS — [01](../evidence/day-08/01-windows-compliance-settings.png) |
| D08-02 | Sign in to Outlook on the managed device; inspect Device info | Device context recognized | Compliant Yes; Managed Yes; Azure AD joined | PASS — [02](../evidence/day-08/02-signin-device-compliant.png) |
| D08-03 | Policy in Report-only; inspect the same sign-in's Report only tab | Compliant-device requirement satisfied without enforcement | RequireCompliantDevice / Report-only: Success | PASS — [03](../evidence/day-08/03-ca-report-only-success.png) |
| D08-04 | Enable CA policy; repeat Outlook sign-in while compliant | Outlook opens; policy succeeds | Overall Success and policy Success at 10:00:42Z; owner confirms Outlook access | PASS — [04](../evidence/day-08/04-ca-compliant-access-success.png) |
| D08-05 | Temporarily set minimum OS above installed version; synchronize and inspect compliance | OS check fails; other checks stay compliant | Minimum OS version Not compliant; Firewall, Antivirus and TPM Compliant | PASS — [05](../evidence/day-08/05-device-noncompliant-os-version.png) |
| D08-06 | With noncompliance and CA On, repeat Outlook sign-in | Access denied because compliance requirement is unmet | Browser displays device-compliance requirement; policy RequireCompliantDevice Failure and overall Failure at 10:23:41Z | PASS — [06](../evidence/day-08/06-ca-noncompliant-access-blocked.png), [07](../evidence/day-08/07-ca-noncompliant-signin-failure.png) |
| D08-07 | Remove temporary OS requirement, synchronize and repeat sign-in with CA still On | Compliance and access recover | Overall and policy Success at 10:36:32Z; owner confirms device Compliant, Outlook opens and CA On | PASS — [08](../evidence/day-08/08-ca-access-restored.png), [owner confirmation](../evidence/day-08/console-excerpts.md) |

Times are UTC on the session date. The installed OS version was owner-reported as 10.0.26300.9457. The guided negative-test threshold was 10.0.26300.9458; screenshot 05 proves the failing setting, not its numeric configuration.

## Not tested / not established

- Emergency-account sign-in and full existing-policy membership audit.
- Other users, devices or Office 365 applications; unmanaged-device access.
- Immediate revocation of an existing session or compliance propagation timing.
- Negative sign-in Device info and specific error code.
- Root cause of the intermediate Policies: Partial success (3 of 4) synchronization message.
- Functional AV/firewall/ASR/BitLocker tests reserved for Day 09.

The tenant-wide no-policy default remains Compliant. PASS here applies only to the tested pilot.
