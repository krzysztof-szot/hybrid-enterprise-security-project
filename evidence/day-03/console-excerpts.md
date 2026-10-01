# Day 03 — Selected text evidence

Transcribed/condensed from owner-supplied output on 2026-10-01, not a new execution. Full screenshots are in the [inventory](README.md).

## LAPS preparation and effective policy

Schema query after Update-LapsADSchema returned:

~~~text
msLAPS-CurrentPasswordVersion
msLAPS-EncryptedDSRMPassword
msLAPS-EncryptedDSRMPasswordHistory
msLAPS-EncryptedPassword
msLAPS-EncryptedPasswordHistory
msLAPS-Password
msLAPS-PasswordExpirationTime
~~~

Set-LapsADComputerSelfPermission returned Workstations. Find-LapsADExtendedRights returned:

~~~text
ExtendedRightHolders:
NT AUTHORITY\SYSTEM
BFL\Domain Admins
BFL\Enterprise Admins
BUILTIN\Administrators
~~~

Selected client events:

~~~text
11:11:32  10018  LAPS successfully updated Active Directory with the new password.
11:11:32  10020  LAPS successfully updated the local admin account with the new password.
                 Account name: labadmin; Account RID: 0x3E9
11:11:32  10004  LAPS policy processing succeeded.
11:13:59  10016  The managed account password does not need to be updated at this time.
11:13:59  10004  LAPS policy processing succeeded.
~~~

Event 10021 reported policy source GPO, backup Active Directory, labadmin, age 30 days, complexity 4, length 20, encryption enabled=1, history=0, DSRM backup=0 and automatic account management=0.

After forced rotation, the DC query returned:

~~~text
ComputerName        : BFL-WKS01
Account             : labadmin
PasswordUpdateTime  : 1.10.2026 11:20:36
ExpirationTimestamp : 31.10.2026 10:20:36
Source              : EncryptedPassword
DecryptionStatus    : Success
~~~

## Negative read validation

After earlier null/incorrect credential attempts were rejected as invalid tests, valid Anna credentials produced:

~~~text
$annaCred.UserName
BFL\anna.finance

Get-ADComputer "BFL-WKS01" -Credential $annaCred
Name
BFL-WKS01
~~~

Get-LapsADPassword with both Credential and DecryptionCredential set to that credential produced no objects. Screenshot 05 records the explicit zero count. Password contents were never requested for publication.

## Audit policy and restored membership

~~~text
Security Group Management   Success
User Account Management     Success and Failure

Applied Group Policy Objects
    Default Domain Controllers Policy
    GPO-BFL-DC-Auditing
    Default Domain Policy
~~~

After the remove/restore test:

~~~text
SamAccountName
adm.onprem
~~~

The immediate event query found no entries. Repeating the read over 30 minutes returned 4729 at 11:37:10; the corrected screenshot is the evidence, not the initial failed-query image.

## KDS, tools and gMSA

Enumerating the Get-KdsRootKey collection yielded RootKeyCount=1, with CreationTime and EffectiveTime both 1.10.2026 11:11:31. The root key's secret material was not displayed. Server time before gMSA creation was 2026-10-01 21:12:18.

RSAT installation returned Online=True, RestartNeeded=False. Install-ADServiceAccount and Test-ADServiceAccount were available.

~~~text
Name    : gmsa-labtask
Enabled : True
PrincipalsAllowedToRetrieveManagedPassword :
  CN=BFL-WKS01,OU=Workstations,OU=Computers,OU=BFL,DC=balticfinance,DC=test

Test-ADServiceAccount -Identity "gmsa-labtask"
True
~~~

Screenshot 07 records LastTaskResult=0 and the service identity in the output file.
