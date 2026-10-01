# Project plan

## Working model

A practical Microsoft hybrid enterprise security portfolio for Baltic Finance. The owner implements each stage; guidance proceeds one meaningful step at a time and waits for results. Days represent milestones rather than guaranteed one-day durations.

## Roadmap

| Day | Goal and scope | Required validation | Status |
|---|---|---|---|
| 01 | Local Windows Server, AD DS, DNS, OU structure, users, security groups | DNS, AD queries, DC health, evidence | Configuration and listed server-side tests completed |
| 02 | AD administration, GPO, password/lockout policy, firewall, screen lock, share mapping, local administrator management | In-scope vs out-of-scope GPO; gpupdate and gpresult | Recorded configuration and tests completed on 2026-10-01; see docs/day-02.md and tests/day-02.md |
| 03 | Separate admin identities, least privilege, Windows LAPS where supported, service account, auditing and group-change monitoring | Standard-user denial vs authorized administration; logs | Recorded scope completed on 2026-10-01; see docs/day-03.md and tests/day-03.md |
| 04 | Cloud Sync, UPN, password hash synchronization, pilot group scope, users and groups | In-scope synchronization; excluded user absent | Recorded scope completed on 2026-10-01; see docs/day-04.md and tests/day-04.md; IE ESC restoration confirmation pending |
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
- The existing Baltic Finance Lab Entra tenant is connected through Cloud Sync. Four users use bfl.-prefixed UPNs under the tenant's onmicrosoft.com suffix to preserve existing cloud identities. The .test AD domain is unchanged.
- BFL-WKS01 was provisioned and AD joined for Day 02 GPO tests, earlier than the original Day 06 client milestone. Plan the client identity lifecycle explicitly: AD domain join and Entra join are different states. Decide on reusing the VM or a separate endpoint before Day 06; do not silently replace its join state.
- BFL-DC01 remains in the built-in Domain Controllers OU.
- adm.onprem belongs to Domain Users and GG-Workstation-Admins; elevated workstation operation passed while Anna was denied. It is excluded from cloud synchronization.
- Windows LAPS manages labadmin with encrypted AD backup; rotation and Anna's lack of read access were tested. gmsa-labtask resides in Service Accounts, is authorized for BFL-WKS01 and runs a manually triggered test task. The Cloud Sync agent uses a separate service identity.
- One NTP source and one DC are lab limitations. VMware host/guest time synchronization settings have not been inspected.
- The owner reported an existing Azure free subscription with USD 200 credit and Entra ID P2. No additional trial or Azure compute resource was created in the recorded steps; balances and expiration dates remain unverified.
- Cloud Sync is a pilot scoped to GG-Finance, GG-IT and GG-Security direct membership, replacing the original OU-scope plan. The old bfl.local configuration was disabled. BFL-DC01 hosts the sole active lab agent.
- Confirm IE ESC was restored to On for Administrators after agent setup; no explicit completion evidence was supplied.

## Daily completion standard

Document objective, context, configuration, security rationale, troubleshooting, result and lessons learned. Record test preconditions, procedure, expected/actual result, status and evidence. Never mark an unperformed test PASS.

## Next action

Confirm IE ESC restoration, then begin Day 05 controlled hybrid identity troubleshooting using the current pilot. Preserve prior exercises and use a reversible change with verification. Do not add empty folders for future work.
