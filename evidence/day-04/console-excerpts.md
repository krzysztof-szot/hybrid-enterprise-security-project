# Day 04 — Text evidence and owner confirmations

This is a record of supplied terminal output and explicitly reported portal checks. It is not an exported provisioning or sign-in log. Tenant-specific identifiers and unrelated account names are omitted; <tenant> denotes the configured tenant suffix.

## Before synchronization

AD queries showed these four users initially had UPNs ending in balticfinance.test and empty mail/proxyAddresses:

~~~text
anna.finance
peter.finance
adam.it
sara.security
~~~

The owner confirmed no corresponding bfl.-prefixed UPNs existed in Entra. After the update, screenshot 01 shows all four new UPNs under the tenant suffix.

The owner reported:
- Old bfl.local configuration disabled; old agent inactive.
- Connect Sync disabled.
- Cloud-only bfl.hybrid.admin created; Hybrid Identity Administrator assigned; login and MFA working.

## Agent prerequisites

~~~text
Name      Status  StartType
VaultSvc  Running Manual

MachinePolicy Undefined
UserPolicy    Undefined
Process       Undefined
CurrentUser   Undefined
LocalMachine  RemoteSigned

.NET Framework Release
533509
~~~

## Administrator exclusion

The on-demand import returned a GUID. Querying that GUID with Get-ADUser produced:

~~~text
SamAccountName    : adm.onprem
DistinguishedName : CN=On-prem Admin,OU=Admins,OU=BFL,DC=balticfinance,DC=test
~~~

Detailed portal output:

~~~text
Active in the source system      True
Scoping filter evaluation passed False
~~~

The generic summary also said Object is in scope and the action was skipped with JoinNotFound. The detailed filter result, correct source-object identification and subsequent absence check support exclusion; the summary alone was insufficient.

## Owner-confirmed functional checks

The owner explicitly reported successful login of the new Anna account using her current AD password. Screenshot 06 independently shows On-premises sync enabled=Yes for that new account.

The owner then confirmed all four requested final checks were correct, with no errors or unexpected results:

1. Three synchronized departmental groups with new project members: Finance has bfl.anna.finance and bfl.peter.finance; IT has bfl.adam.it; Security has bfl.sara.security.
2. adm.onprem and tst.lockout absent from Entra.
3. Original anna.finance and peter.finance accounts remain On-premises sync enabled=No.
4. New Cloud Sync configuration has the expected status.

These are OWNER-CONFIRMED observations. No extra screenshot or raw export was supplied for these four final checks. IE ESC restoration was requested but not explicitly confirmed.
