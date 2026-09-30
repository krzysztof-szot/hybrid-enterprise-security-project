# Project plan

## Working model

A practical Microsoft hybrid enterprise security portfolio for Baltic Finance. The owner implements each stage; guidance proceeds one meaningful step at a time and waits for results. Days represent milestones rather than guaranteed one-day durations.

## Roadmap

| Day | Goal and scope | Required validation | Status |
|---|---|---|---|
| 01 | Local Windows Server, AD DS, DNS, OU structure, users, security groups | DNS, AD queries, DC health, evidence | Configuration and listed server-side tests completed |
| 02 | AD administration, GPO, password/lockout policy, firewall, screen lock, share mapping, local administrator management | In-scope vs out-of-scope GPO; gpupdate and gpresult | Not started |
| 03 | Separate admin identities, least privilege, Windows LAPS where supported, service account, auditing and group-change monitoring | Standard-user denial vs authorized administration; logs | Not started; dedicated account created without privileges on Day 01 |
| 04 | Entra Connect or Cloud Sync, UPN, password hash synchronization, OU scope, users and groups | In-scope synchronization; excluded user absent | Not started |
| 05 | Controlled hybrid identity troubleshooting | Problem → Investigation → Root Cause → Resolution → Verification | Not started |
| 06 | Windows 11, Entra Join, Intune enrollment, ownership and primary user | Device identity, join type and enrollment verification | Not started |
| 07 | Justified Intune configuration profiles and security baseline | Policy assignment and endpoint results | Not started |
| 08 | Compliance plus Conditional Access requiring compliant devices | Allowed compliant device; blocked non-compliant device; sign-in logs | Not started |
| 09 | Defender Antivirus, firewall, BitLocker, ASR where available; optional MDE | Endpoint security configuration and tests | Not started |
| 10 | Azure RBAC: Reader, Contributor, Security Reader, Storage Blob Data Reader | Authorized operation vs denied change; distinguish Entra roles and Azure RBAC | Not started |
| 11 | Key Vault, managed identity and access control | Authorized secret read vs denied identity; no hard-coded credentials | Not started |
| 12 | VNet, subnets, NSGs; temporary test resource only when needed | Allowed and blocked traffic; resource cleanup | Not started |
| 13 | Defender for Cloud recommendations and posture | Finding → Risk → Recommendation → Remediation → Verification | Not started |
| 14 | Log Analytics, Sentinel and useful KQL for selected telemetry | Data ingestion and detection queries with real results | Not started |
| 15 | Capstone using existing services | Identity/Azure and endpoint/Zero Trust scenarios: Prevent → Detect → Investigate → Respond → Verify | Not started |
| Final | Security assessment | Strengths, residual risks, lab limitations and production improvements | Not started |

## Current decisions and dependencies

- AD domain: balticfinance.test; NetBIOS: BFL. No public domain is owned.
- The Entra tenant and actual onmicrosoft.com UPN suffix have not been established in this session. Resolve these before synchronization; do not claim that .test is a verified cloud domain.
- A domain-joined Windows client is needed before Day 02 GPO tests, earlier than the original Day 06 client milestone. First inspect licensing/media and available RAM/disk. Plan the client identity lifecycle explicitly: AD domain join and Entra join are different states. Decide on reusing the VM or a separate endpoint before Day 06; do not silently replace its join state.
- BFL-DC01 remains in the built-in Domain Controllers OU.
- adm.onprem has only Domain Users membership. GG-Workstation-Admins is empty and grants no workstation privileges yet.
- Service Accounts OU is empty. LAPS, service accounts and hardening remain future work.
- One NTP source and one DC are lab limitations. VMware host/guest time synchronization settings have not been inspected.
- Azure trials and paid resources have not been activated as part of the recorded work.

## Daily completion standard

Document objective, context, configuration, security rationale, troubleshooting, result and lessons learned. Record test preconditions, procedure, expected/actual result, status and evidence. Never mark an unperformed test PASS.

## Next action

Upload and review Day 01 screenshots against the evidence checklist. Then prepare a supported local Windows 11 client for AD join and Day 02 tests. Do not add empty folders for future work.
