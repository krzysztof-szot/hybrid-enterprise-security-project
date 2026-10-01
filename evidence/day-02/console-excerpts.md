# Day 02 — Selected console evidence

Selected output transcribed from the owner's session on 2026-10-01. These excerpts are not full logs or new executions. Formatting is condensed; narrative observations are labelled separately.

## Effective security settings on BFL-WKS01

Get-ItemPropertyValue for InactivityTimeoutSecs returned:

~~~text
900
~~~

Get-NetFirewallProfile -PolicyStore ActiveStore:

~~~text
Name    Enabled DefaultInboundAction DefaultOutboundAction
Domain     True                Block                 Allow
Private    True                Block                 Allow
Public     True                Block                 Allow
~~~

## Final computer GPO application

gpresult /scope computer /r, created 10/1/2026 at 10:20:29 AM:

~~~text
RSOP data for  on BFL-WKS01 : Logging Mode
Group Policy was applied from: BFL-DC01.balticfinance.test

Applied Group Policy Objects
    GPO-BFL-Workstation-Security
    GPO-BFL-Workstation-LocalAdmins
    Default Domain Policy
~~~

## Negative drive-map test in Sara's session

~~~text
PS> whoami
bfl\sara.security
PS> net use F:
The network connection could not be found.
More help is available by typing NET HELPMSG 2250.
~~~

User policy refresh immediately before this check completed successfully.

## Temporary account cleanup

After Disable-ADAccount -Identity "tst.lockout":

~~~text
SamAccountName Enabled
tst.lockout      False
~~~

The owner confirmed ten intentional failed attempts and subsequent correct-password access. Event 4740 is preserved in screenshot 06. No separate timed automatic-unlock result was supplied.

## Share configuration

~~~text
Name    Path                 EncryptData
Finance C:\LabShares\Finance        True

AccountName       AccessControlType AccessRight
BFL\Domain Admins Allow             Full
BFL\GG-Finance    Allow             Change
~~~

icacls returned:

~~~text
C:\LabShares\Finance BUILTIN\Administrators:(F)
                     BFL\GG-Finance:(OI)(CI)(M)
                     BUILTIN\Administrators:(OI)(CI)(F)
                     NT AUTHORITY\SYSTEM:(OI)(CI)(F)
Successfully processed 1 files; Failed processing 0 files
~~~

## Drive-map configuration defect

The supplied Drives.xml contained this targeting element:

~~~xml
<FilterGroup bool="AND" not="0" name="BFL\GG-Finance" sid="" userContext="1" primaryGroup="0" localGroup="0"/>
~~~

After reselecting the group using Check Names, the owner supplied screenshot 09 showing successful mapping and file access. No post-repair XML was provided. Event queries in the selected time windows returned no matching records; this was not treated as proof of correct processing.

## Owner observations: session lock

The first attempt was inconclusive: the guest display went dark and VMware later offered Resume this VM. On the repeat test, guest sleep/display timers were temporarily disabled. The owner reported that after 15 minutes Anna's desktop changed to the clock/lock screen and login required her password. The owner then confirmed restoring power settings. No screenshot or event export independently attributes that lock to the GPO.

## Anna's password state

Get-ADUser returned PasswordLastSet=1.10.2026 07:43:22 and a nonzero pwdLastSet. This documents a password being set after the Day 01 initial-change requirement; it is not a screenshot of the password-change dialog.
