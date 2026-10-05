# Day 11 — Key Vault access-control tests

**Executed by:** project owner in Azure Automation Test pane, Run on: Azure.
**Status:** four recorded functional tests passed. No new Azure tests were run while preparing this documentation.
**Evidence:** [7 screenshots](../evidence/day-11/README.md) · [Session excerpts](../evidence/day-11/console-excerpts.md).

## Preconditions

- New kv-bfl-day11-01 vault with an Enabled bfl-day11-test-secret version (01).
- System-assigned managed identity enabled on aa-bfl-day11 (02).
- Read and write-negative runbooks use Connect-AzAccount -Identity and explicitly pass its context.
- Administrator setup and managed-identity execution are separate.
- Read runbook is unchanged across the initial, authorized and revoked tests.

## Functional results

| ID | Preconditions and procedure | Expected result | Actual result / status | Evidence |
|---|---|---|---|---|
| KV-01 | No secret-read role for aa-bfl-day11; run rb-bfl-day11-keyvault-read | Sign-in succeeds; secret read denied | **PASS:** sign-in marker, Failed, Forbidden and caller not authorized | [03](../evidence/day-11/03-keyvault-read-denied.png) |
| KV-02 | Assign Key Vault Secrets User to aa-bfl-day11 at vault scope; rerun read | Secret retrieval succeeds without exposing its value | **PASS:** Completed and SUCCESS marker after non-null SecretValue check | [04](../evidence/day-11/04-keyvault-secrets-user-assignment.png), [05](../evidence/day-11/05-keyvault-read-success.png) |
| KV-03 | Keep the reader role; run rb-bfl-day11-keyvault-write-denied to create bfl-day11-write-test | Secret creation rejected | **PASS:** sign-in and write-attempt markers; Forbidden, not the script's unexpected-success exception | [06](../evidence/day-11/06-keyvault-write-denied.png) |
| KV-04 | Remove the reader assignment; rerun unchanged read runbook | Subsequent secret read denied again | **PASS:** sign-in marker followed by Forbidden | [07](../evidence/day-11/07-keyvault-access-revoked.png) |

Failed is the expected job status for the negative tests because ErrorActionPreference is Stop. PASS is based on the specific authorization error, not simply on a failed job.

## Supporting observations

| Observation | Evidence / boundary |
|---|---|
| Secret version Enabled | [01](../evidence/day-11/01-keyvault-test-secret.png); plaintext not displayed |
| Automation identity On | [02](../evidence/day-11/02-automation-managed-identity.png) |
| Read role belongs to the managed identity at vault scope | [04](../evidence/day-11/04-keyvault-secrets-user-assignment.png); This resource on kv-bfl-day11-01 |
| No sign-in credentials embedded in code | Supplied [runbook code](../docs/day-11.md); no deployed-source export provided |
| Secret value not printed by read code | Code inspection plus screenshot 05; no plaintext comparison performed |

## Limits

- Evidence supports this identity, vault and secret, not every role or operation.
- No separate role-removal screenshot or full effective-permissions export; the repeated denial verifies the functional effect of the recorded removal procedure.
- Write test targets creation of a separate secret; modification and deletion were not tested.
- No scheduler, published-job execution, diagnostic-log query, private-endpoint test or exact propagation measurement.
- No actual cost measurement or final settings/runtime export.
- Screenshots do not establish precise test dates/times. Documentation updated 2026-10-05.

[Implementation and troubleshooting](../docs/day-11.md)
