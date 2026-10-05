# Day 11 — Selected runbook output

These excerpts transcribe visible output from the owner's screenshots. They are not newly executed commands or full exported job streams.

[Implementation and runbook code](../../docs/day-11.md) · [Tests](../../tests/day-11.md) · [Screenshots](README.md)

## Initial read — screenshot 03

Runbook: rb-bfl-day11-keyvault-read. Status: Failed.

```text
Managed identity sign-in completed.
Code: Forbidden
Message: Caller is not authorized to perform action on resource.
```

Output and error streams are grouped here for readability; their display placement is not a precise event timeline.

## Authorized read — screenshot 05

Runbook: rb-bfl-day11-keyvault-read. Status: Completed.

```text
Managed identity sign-in completed.
SUCCESS: Secret retrieved. Its value is not displayed.
```

Screenshot 04 records Key Vault Secrets User assigned to aa-bfl-day11 at the vault scope before this test. The supplied script checks SecretValue without printing it.

## Write denial — screenshot 06

Runbook: rb-bfl-day11-keyvault-write-denied. Status: Failed.

```text
Managed identity sign-in completed.
Attempting to create a separate test secret.
Code: Forbidden
Message: Caller is not authorized to perform action on resource.
```

The observed error is an authorization rejection. It is not the script's UNEXPECTED exception, which would indicate that the write succeeded.

## Read after revocation — screenshot 07

Runbook: rb-bfl-day11-keyvault-read. Status: Failed.

```text
Managed identity sign-in completed.
Code: Forbidden
Message: Caller is not authorized to perform action on resource.
```

This repeated request followed removal of the vault-scoped reader assignment. The screenshot demonstrates the resulting denial, not the exact propagation delay.

## Setup observations

The secret version is Enabled (01), and the Automation account's system-assigned identity is On (02). The owner reported an Automation creation error in North Europe stating that only one account is allowed per subscription per region; guided provisioning moved to East US. These setup reports are distinct from the four captured functional test outcomes.
