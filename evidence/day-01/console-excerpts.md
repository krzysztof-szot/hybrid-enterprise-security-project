# Day 01 — selected actual console output

Provenance: owner-supplied PowerShell output from the 2026-09-30 guided session. These are selected excerpts, not complete raw transcripts, new executions, or Windows event-log exports. Whitespace is normalized; omitted lines are not presented as test output. Password prompts, physical addresses and unnecessary identifiers are excluded. No screenshots are embedded.

## Network before promotion

```text
IPAddress     PrefixLength AddressState
---------     ------------ ------------
192.168.58.10           24    Preferred

ComputerName     : www.microsoft.com
RemoteAddress    : 23.197.162.102
RemotePort       : 443
InterfaceAlias   : Ethernet0
SourceAddress    : 192.168.58.10
TcpTestSucceeded : True
```

## Domain and forest

```text
bfl\administrator

DNSRoot            NetBIOSName        DomainMode
-------            -----------        ----------
balticfinance.test BFL         Windows2025Domain

RootDomain                ForestMode
----------                ----------
balticfinance.test Windows2025Forest
```

## Services and shares

```text
Name      Status
----      ------
ADWS     Running
DNS      Running
Kdc      Running
Netlogon Running
NTDS     Running

Name     Path
----     ----
NETLOGON C:\WINDOWS\SYSVOL\sysvol\balticfinance.test\SCRIPTS
SYSVOL   C:\WINDOWS\SYSVOL\sysvol
```

## DNS

Get-DnsClientServerAddress returned 127.0.0.1. Selected forwarder, external query and dcdiag output:

```text
IPAddress   : 192.168.58.2
UseRootHint : True

Name       : e13678.dscb.akamaiedge.net
QueryType  : A
TTL        : 4
Section    : Answer
IP4Address : 23.197.162.102

Domain: balticfinance.test
               BFL-DC01                     PASS PASS n/a  n/a  n/a  n/a  n/a
```

The dcdiag columns were Auth, Basc, Forw, Del, Dyn, RReg, Ext. Only Auth and Basc were exercised by that DNS test. The separate SRV query returned bfl-dc01.balticfinance.test, priority 0, weight 100, port 389, with additional A record 192.168.58.10.

## Users

Selected output for the dedicated administration account:

```text
SamAccountName       : adm.onprem
UserPrincipalName    : adm.onprem@balticfinance.test
Enabled              : True
DistinguishedName    : CN=On-prem Admin,OU=Admins,OU=BFL,DC=balticfinance,DC=test
pwdLastSet           : 0
PasswordNeverExpires : False

Name         GroupScope GroupCategory
----         ---------- -------------
Domain Users     Global      Security
```

The supplied four-user query also confirmed Enabled True, pwdLastSet 0 and PasswordNeverExpires False for adam.it, anna.finance, peter.finance and sara.security, in their respective departmental OUs.

## Groups

```text
Group                 Count Members
-----                 ----- -------
GG-Finance                2 anna.finance, peter.finance
GG-IT                     1 adam.it
GG-Security               1 sara.security
GG-Workstation-Admins     0
```

## DC diagnostics

Selected result lines from the requested tests:

```text
BFL-DC01 passed test Connectivity
BFL-DC01 passed test Advertising
BFL-DC01 passed test SysVolCheck
BFL-DC01 passed test NetLogons
BFL-DC01 passed test Services
```

## Time before repair

```text
ReferenceId: 0x4C4F434C (source name:  "LOCL")
Last Successful Sync Time: 30.09.2026 17:32:06
Source: Local CMOS Clock

Type: NT5DS (Local)

Peer:
State: Pending
```

## Time after repair

```text
Leap Indicator: 0(no warning)
Stratum: 5 (secondary reference - syncd by (S)NTP)
Last Successful Sync Time: 30.09.2026 17:59:09
Source: time.windows.com,0x8

Peer: time.windows.com,0x8
State: Active
Mode: 3 (Client)
Stratum: 4 (secondary reference - syncd by (S)NTP)
```

## Time zone and activation

```text
Central European Standard Time

Windows(R), ServerStandardEval edition:
    Timebased activation will expire 29.03.2027 15:10:07
```
