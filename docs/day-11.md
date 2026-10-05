# Day 11 — Key Vault and managed identity access control

**Status:** recorded functional scope completed. **Documentation updated:** 2026-10-05.

[Tests](../tests/day-11.md) · [7 screenshots](../evidence/day-11/README.md) · [Session excerpts](../evidence/day-11/console-excerpts.md)

## Objective and context

Retrieve a non-sensitive test secret through an Azure Automation system-assigned managed identity without storing sign-in credentials in code. Verify the same identity before permission is granted, with read-only permission, during a write attempt, and after permission is revoked.

The owner performed the Azure actions manually. This milestone used only new resources; earlier VMs, applications and Automation resources were not reused or changed. Tenant Bootstrap Administrator performed setup. The runbooks authenticated as the Automation account's managed identity, not as the administrator.

## Lab resources and configuration

| Item | Value used in the guided lab |
|---|---|
| Directory / subscription | Baltic Finance Lab / Azure subscription 1 |
| Resource group | rg-bfl-keyvault-lab |
| Key Vault | kv-bfl-day11-01 |
| Test secret | bfl-day11-test-secret |
| Automation account | aa-bfl-day11 |
| Identity | System assigned |
| Runtime environment | rt-bfl-day11-ps74; PowerShell 7.4 with Az packages |
| Read runbook | rb-bfl-day11-keyvault-read |
| Write-negative runbook | rb-bfl-day11-keyvault-write-denied |
| Execution | Test pane, Run on: Azure |

The guided region choice was Poland Central for the group/vault and East US for the new Automation account after the provisioning errors described below. The owner confirmed creation; the supplied screenshots independently verify the named vault/secret, Automation identity and runbook results, but do not provide a complete deployment or runtime configuration export.

The vault setup instructions specified Standard tier, Azure RBAC, seven-day soft delete, purge protection and public access from all networks without a private endpoint. These are recorded setup choices, not an independently verified settings audit. Public access kept this exercise focused on identity authorization. Private connectivity and network isolation were not tested.

A non-sensitive demonstration value was stored in the secret. Its plaintext is not reproduced here. Screenshot 01 shows an Enabled current version. Screenshot 02 shows the Automation system-assigned identity On.

The owner reconfirmed EUR 159.53 subscription credit with expiry 2026-10-13 during preparation. This is a session report, not current billing verification. Key Vault operations and Automation runtime were considered before provisioning; actual charges and total runtime were not measured.

## Permission sequence

| Stage | Identity / permission | Scope and purpose |
|---|---|---|
| Secret preparation | Administrator / Key Vault Secrets Officer, guided setup | Vault; create the demonstration secret |
| Initial read | aa-bfl-day11 / no secret-read role | Establish an actual authorization denial |
| Authorized read | aa-bfl-day11 / Key Vault Secrets User | kv-bfl-day11-01 only; screenshot 04 shows Managed identity and This resource |
| Write attempt | Same Key Vault Secrets User assignment | Verify that secret creation remains denied |
| Revocation | Remove that role assignment and repeat the read | Verify denial on a new request |

The scope in screenshot 04 is the vault, not the subscription. The role permits reading secret values and metadata but does not grant secret creation or modification. This separation is defined in the [Microsoft role documentation](https://learn.microsoft.com/en-us/azure/role-based-access-control/built-in-roles/security#key-vault-secrets-user).

No client secret, password, certificate or access token was embedded in the runbooks. Resource names are identifiers, not authentication credentials. Authentication used Connect-AzAccount -Identity; authorization depended on the vault role assignment.

## Read runbook

The following code was supplied for rb-bfl-day11-keyvault-read and used for the before/after tests. It is a session code record, not an exported Automation artifact or a newly executed test.

```powershell
$ErrorActionPreference = 'Stop'

Disable-AzContextAutosave -Scope Process | Out-Null

$context = (Connect-AzAccount -Identity -SkipContextPopulation).Context
Write-Output 'Managed identity sign-in completed.'

$secret = Get-AzKeyVaultSecret `
    -VaultName 'kv-bfl-day11-01' `
    -Name 'bfl-day11-test-secret' `
    -DefaultProfile $context

if ($null -eq $secret.SecretValue) {
    throw 'Secret value was not returned.'
}

Write-Output 'SUCCESS: Secret retrieved. Its value is not displayed.'
```

Disabling context autosave prevents inherited saved context from being used. The returned context is passed explicitly to the Key Vault command. The secret is retained in a variable and its SecureString property is checked; the secret object and value are not written to output.

Before role assignment, screenshot 03 shows successful managed-identity sign-in followed by **Forbidden: Caller is not authorized to perform action on resource**. After assignment, screenshot 05 shows **Completed** and the success marker. This confirms authenticated access changed when authorization changed. It does not compare the secret's plaintext against an expected value.

## Write-negative runbook

A separate runbook kept the read test unchanged. It attempted to create a different secret with a generated test value.

```powershell
$ErrorActionPreference = 'Stop'

Disable-AzContextAutosave -Scope Process | Out-Null

$context = (Connect-AzAccount -Identity -SkipContextPopulation).Context
Write-Output 'Managed identity sign-in completed.'

$testValue = ConvertTo-SecureString `
    -String ([guid]::NewGuid().ToString()) `
    -AsPlainText -Force

Write-Output 'Attempting to create a separate test secret.'

Set-AzKeyVaultSecret `
    -VaultName 'kv-bfl-day11-01' `
    -Name 'bfl-day11-write-test' `
    -SecretValue $testValue `
    -DefaultProfile $context | Out-Null

throw 'UNEXPECTED: Secret creation succeeded. Review permissions.'
```

Screenshot 06 shows the sign-in and attempt markers followed by **Forbidden**, as expected for this reader role. A Failed runbook alone would not establish a pass: the final throw also fails the runbook if creation unexpectedly succeeds. The actual authorization error is the decisive evidence. The original secret was not targeted by this write attempt.

## Revocation test

The role assignment was removed through the vault's IAM page as instructed, then the unchanged read runbook was run again. Screenshot 07 shows sign-in completed but secret retrieval denied with **Forbidden**.

The result verifies denial on a subsequent request. It does not measure exact propagation time or erase a value previously read into a process. No plaintext value was printed in the recorded tests.

## Troubleshooting and lessons

| Problem / observation | Investigation and resolution | Verification |
|---|---|---|
| Automation creation in North Europe was rejected: only one account per subscription per region | An existing account already used that region; choose a supported alternative for the new lab account | New aa-bfl-day11 identity and working runbooks shown |
| Poland Central creation also failed; owner attributed this to trial restrictions | Guided creation moved to East US; no existing account was reused | Creation owner-confirmed; region is not independently visible in the test screenshots |
| Initial read returned Forbidden after sign-in | Authentication succeeded; add the scoped secret-reader role rather than change credentials | Same read code then Completed |
| Write runbook status was Failed | Inspect the specific error rather than interpreting status alone | Forbidden at the write attempt confirms the negative test |
| Read became Forbidden after removing the role | Repeat a new request using the same identity and code | Screenshot 07 confirms loss of read access |

The practical distinction is between an identity being able to authenticate and being authorized for a particular data operation. Granting read access did not authorize writing; removing that grant prevented another read.

## Evidence limits and next milestone

Seven screenshots support the recorded configuration observations and four functional tests. They show Azure Automation Test pane executions, not scheduled production jobs. Publishing, scheduling, exact Az package versions, full role exports, diagnostic-log ingestion, secret rotation, delete/overwrite denial, cross-vault isolation and private networking were not independently tested. Exact execution timestamps are not visible in these screenshots; the documentation date is not presented as a verified test timestamp.

Next is **Day 12: VNet, subnets and NSGs**. Establish an isolated, cost-reviewed network test scope and capture allowed and blocked traffic. Earlier resources are not assumed available for reuse.

## References

- [Managed identity in Azure Automation](https://learn.microsoft.com/en-us/azure/automation/enable-managed-identity-for-automation)
- [Key Vault Azure RBAC guide](https://learn.microsoft.com/en-us/azure/key-vault/general/rbac-guide)
- [Set-AzKeyVaultSecret](https://learn.microsoft.com/en-us/powershell/module/az.keyvault/set-azkeyvaultsecret)
