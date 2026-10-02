# Day 05 — Supplied console and portal excerpts

Transcribed from the owner's session. Formatting is normalized; the tenant suffix is replaced with a placeholder. These excerpts are not newly executed tests.

## Membership before disabling

```powershell
Get-ADPrincipalGroupMembership "tst.sync" | Select-Object Name
```

```text
Name
----
Domain Users
GG-IT
```

## Disable in AD and verify

```powershell
Disable-ADAccount -Identity "tst.sync" -ErrorAction Stop
Get-ADUser "tst.sync" | Select-Object SamAccountName, Enabled
```

```text
SamAccountName Enabled
-------------- -------
tst.sync         False
```

## Copied Cloud Sync export details

```text
User 'bfl.tst.sync@<tenant>.onmicrosoft.com' was updated in Microsoft Entra ID

Target attribute name:  AccountEnabled
Target attribute value: False
```

This supports propagation of the disabled state. It does not establish denied interactive login or revocation of existing sessions. GG-IT membership was retained to keep the account in synchronization scope.

[Evidence inventory](README.md) · [Test record](../../tests/day-05.md)
