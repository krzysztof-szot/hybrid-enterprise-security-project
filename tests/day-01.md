# Day 01 — test record

Executed by the owner on 2026-09-30. PASS means the stated check was performed and its reported result matches the expectation; it does not certify the whole environment. Evidence consists of [selected supplied console output](../evidence/day-01/console-excerpts.md) and observations identified below. [Published screenshots](../evidence/day-01/README.md).

| ID | Preconditions | Procedure | Expected result | Actual result | Status | Evidence |
|---|---|---|---|---|---|---|
| D01-01 | Static IPv4 applied on Ethernet0 | ipconfig /all; Get-NetIPAddress | 192.168.58.10/24, DHCP off, gateway .2 | Matched; Preferred address state | PASS | Supplied ipconfig; console excerpts: Network before promotion |
| D01-02 | Internet via VMware NAT, before promotion | Resolve-DnsName www.microsoft.com; Test-NetConnection -Port 443 | Name resolves; TCP succeeds | A record returned; TcpTestSucceeded True | PASS | Network before promotion |
| D01-03 | Promotion completed and server restarted | whoami; Get-ADDomain; Get-ADForest | BFL login; expected domain/forest and levels | bfl\\administrator; balticfinance.test; Windows2025Domain/Forest | PASS | Domain and forest |
| D01-04 | DC started | Get-Service NTDS,DNS,Netlogon,Kdc,ADWS | All Running | All Running | PASS | Services and shares |
| D01-05 | SYSVOL initialized | Get-SmbShare SYSVOL,NETLOGON | Both shares exist | Both returned with domain paths | PASS | Services and shares |
| D01-06 | Local DNS available | Resolve-DnsName _ldap._tcp.dc._msdcs.balticfinance.test -Type SRV -Server 192.168.58.10 | DC target and LDAP port 389 | Correct target, port and additional A record | PASS | DNS |
| D01-07 | DNS forwarder configured | Get-DnsServerForwarder; Resolve-DnsName www.microsoft.com -Type A -DnsOnly -Server 192.168.58.10 | External answer through DC DNS | Forwarder .2; CNAME/A response | PASS | DNS; this does not separately prove which upstream path was used |
| D01-08 | Elevated session on DC | dcdiag /test:DNS /DnsBasic /v | Connectivity, Auth and Basc pass | PASS; other DNS columns n/a | PASS | DNS; no claim for unexecuted subtests |
| D01-09 | OUs created | Get-ADOrganizationalUnit with SearchBase under BFL | Required hierarchy and protection | Initial missing departments detected, then corrected; first ten protection values True; Groups creation succeeded | PASS for presence; Groups protection re-query pending | Supplied OU outputs recorded in Day 01 notes; published final tree screenshot |
| D01-10 | Five users created | Get-ADUser with Department, pwdLastSet, PasswordNeverExpires | Correct locations, enabled, first-change required | Matched for four staff and adm.onprem | PASS | Users excerpt and supplied per-user query summarized there |
| D01-11 | Groups created and members added | Get-ADGroup; Get-ADGroupMember | Four Global/Security groups; counts 2/1/1/0 | Matched | PASS | Groups; supplied creation verification |
| D01-12 | adm.onprem created without privilege assignment | Get-ADPrincipalGroupMembership adm.onprem | Domain Users only | Domain Users only | PASS | Users; membership check, not a negative authorization test |
| D01-13 | DC operational | dcdiag /test:Advertising /test:SysVolCheck /test:NetLogons /test:Services | Requested tests pass | Connectivity and all four requested tests passed | PASS | DC diagnostics |
| D01-14 | PDC role and W32Time inspected | Get-ADDomain PDCEmulator; w32tm configuration/status/peers; configure external NTP and repeat | External source, successful sync and Active client peer | Before: local clock/NT5DS/Pending. After: time.windows.com, Active, last sync 17:59:09 | PASS after remediation | Time before repair / Time after repair |
| D01-15 | Time-zone correction applied | Get-TimeZone | Central European Standard Time | Matched | PASS | Time zone and activation |
| D01-16 | Evaluation installed and online | slmgr.vbs /xpr | Active evaluation with future expiration | Expiration 29.03.2027 15:10:07 as displayed | PASS | Time zone and activation |

## Not performed / not independently verified on Day 01

Client and GPO follow-up tests are recorded separately in [Day 02](day-02.md).

| Check | Status and reason |
|---|---|
| Windows Update final patch inventory | OWNER CONFIRMED completion; no final KB/UBR output captured |
| Domain join and ordinary-user logon from Windows client | NOT RUN — client not yet provisioned |
| First-logon password change | NOT RUN — pwdLastSet setting verified only |
| Positive/negative resource authorization | NOT RUN — no protected resource scenario yet |
| GPO application and scope exclusion | NOT RUN — Day 02 |
| Replication with another DC | NOT RUN — single DC |
| Backup and restore | NOT RUN |
| Full dcdiag / all DNS subtests | NOT RUN — only listed subsets executed |
| Event-log verification | NOT RUN — no event-log export supplied |
| NTP persistence after reboot/suspend | NOT RUN |
| Screenshot publication | COMPLETE — seven images published |

## Useful commands for reproducing the completed checks

Run on BFL-DC01 with appropriate privileges; this block is a reference, not a claim of an additional execution.

```powershell
Get-ADDomain | Select-Object DNSRoot, NetBIOSName, DomainMode, PDCEmulator
Get-ADForest | Select-Object RootDomain, ForestMode
Get-DnsServerForwarder | Format-List IPAddress, UseRootHint
Resolve-DnsName _ldap._tcp.dc._msdcs.balticfinance.test -Type SRV -Server 192.168.58.10
Resolve-DnsName www.microsoft.com -Type A -DnsOnly -Server 192.168.58.10
dcdiag /test:DNS /DnsBasic /v
dcdiag /test:Advertising /test:SysVolCheck /test:NetLogons /test:Services
w32tm /query /status
w32tm /query /peers
```
