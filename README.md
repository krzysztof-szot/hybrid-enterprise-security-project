# hybrid-enterprise-security-project

A hands-on Microsoft hybrid security lab for the fictional organization **Baltic Finance**. The lab is built and operated manually by the project owner, with guided review and troubleshooting.

## Current status

**Day 01 and the recorded Day 02 configuration and tests completed. Latest lab session: 2026-10-01.**

Implemented: one local Windows Server 2025 domain controller, AD DS, DNS, organizational units, users, security groups, and external time synchronization. Day 02 adds a domain-joined Windows 11 workstation, scoped GPOs, password/lockout policy, session-lock testing, Finance share authorization and mapping, and local administrator group management. No Azure, Entra synchronization, Intune, Defender cloud integration, or Sentinel deployment has been completed.

- [Day 01 implementation and troubleshooting](docs/day-01.md)
- [Day 01 test record](tests/day-01.md)
- [Evidence inventory and screenshot checklist](evidence/day-01/README.md)
- [Selected actual console output](evidence/day-01/console-excerpts.md)
- [Day 02 implementation and troubleshooting](docs/day-02.md)
- [Day 02 test record](tests/day-02.md)
- [Day 02 screenshots and descriptions](evidence/day-02/README.md)
- [Day 02 console evidence](evidence/day-02/console-excerpts.md)
- [Project roadmap](PROJECT-PLAN.md)

## Implemented architecture

```mermaid
flowchart LR
    Host["Local laptop / VMware Workstation Pro"] --> NAT["VMnet8 NAT: 192.168.58.0/24"]
    NAT --> DC["BFL-DC01: 192.168.58.10\nAD DS + DNS / balticfinance.test"]
    NAT --> WKS["BFL-WKS01: Windows 11 Enterprise Evaluation\nDHCP; DNS 192.168.58.10"]
    WKS -->|"AD logon / GPO / Finance SMB share"| DC
    DC -->|"External DNS forwarding"| DNS["VMware DNS proxy: 192.168.58.2"]
    DC -->|"NTP client"| NTP["time.windows.com"]
```

The DC's IPv4 DNS client uses 127.0.0.1. NAT provides outbound connectivity; it is not a complete isolation boundary.

## Target architecture — not yet implemented

On-premises AD → Hybrid Identity → Microsoft Entra ID → Intune / Endpoint Security → Azure Security → Defender → Log Analytics → Microsoft Sentinel → Detection / Investigation / Response.

The project emphasizes least privilege, justified security settings, positive and negative tests, log verification, troubleshooting, and cost control.

## Evidence and limitations

PASS applies only to tests actually performed and supported by supplied output. Published console excerpts were transcribed from the owner's session; they are not new automated test runs. Seven Day 01 and ten Day 02 screenshots are published. Client logon, GPO scope, Finance allow/deny access and drive mapping were tested. The session-lock test is an owner observation. GG-Workstation-Admins is configured locally but remains empty; member privileges have not been functionally tested. Backup/restore and replication between multiple DCs remain untested.

This is a single-DC educational lab, not a production-ready or comprehensively secured environment. Passwords, DSRM credentials, tokens, VM disks, and installation media must not be committed.
