# Day 10 — Selected session and portal excerpts

**Date:** 2026-10-02. These are transcribed observations and owner confirmations, not newly executed console commands. Full command output, Activity Log exports and storage diagnostic logs were not supplied.

[Implementation](../../docs/day-10.md) · [Tests](../../tests/day-10.md) · [Screenshots](README.md)

## Owner-confirmed prerequisites

- Azure subscription 1: Active, in Baltic Finance Lab.
- Tenant Bootstrap Administrator: Owner at the subscription scope.
- Remaining credit: EUR 159.53; expiry October 13, 2026. Offer type not identified.
- rg-bfl-rbac-lab created according to the guided settings; region Poland Central.
- Actual storage account name: stbflrbaclab01.
- Administrator assigned Storage Blob Data Contributor; container authentication Microsoft Entra user account; test file uploaded.

These are session confirmations, not an independent subscription or billing audit.

## Resource tags

| Stage | Attempted RbacTest value | Observed outcome |
|---|---|---|
| Reader | ReaderAttempt | AuthorizationFailed |
| Reader + Contributor | ContributorAllowed | Resulting tag shown |
| Reader + Security Reader, after Contributor removal | SecurityReaderAttempt | AuthorizationFailed |

Screenshot 05 shows two active permanent assignments, Contributor and Reader, on the resource group. The Add role assignment option is disabled. No API-denial response was captured for this operation.

## Defender for Cloud

Screenshot 06 shows active Security Reader on Azure subscription 1. Screenshot 07 shows Recommendations for that subscription and multiple rows marked Not evaluated. This records read access, not completed assessment or remediation.

## Blob read, write and revocation

Authentication method in screenshots 09–12: **Microsoft Entra user account**.

Initial denial banner in screenshot 09 states that the user lacks permission to list data using Microsoft Entra ID. Embedded UTC time: **2026-10-02T19:00:04.0876148Z**.

Screenshot 10 shows rbac-test.txt as a **34 B**, Hot (Inferred), Block blob and the browser download. The owner confirmed the downloaded text:

```text
Baltic Finance - Day 10 RBAC test.
```

Screenshot 11 reports a failed upload of anna-upload-test.txt because the request is not authorized to perform the operation with the permission in use.

Screenshot 12 repeats the Entra listing denial after removal of Anna's data-reader role. Embedded UTC time: **2026-10-02T19:12:32.0834202Z**. These two timestamps bound different test stages; they do not measure role-assignment propagation.

## Cleanup

The owner explicitly confirmed removal of Security Reader for Anna. Screenshot 13 shows the refreshed resource-group list without rg-bfl-rbac-lab; NetworkWatcherRG and rg-bfl-identity-lab remain listed.

This evidence supports the recorded cleanup of the temporary lab scope. It is not a full exported inventory of all resources, assignments or billable usage.
