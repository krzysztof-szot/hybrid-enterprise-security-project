# Project plan

## Working model

A practical Microsoft hybrid enterprise security portfolio for Baltic Finance. The owner implements each stage; guidance proceeds one meaningful step at a time and waits for results. Days represent milestones rather than guaranteed one-day durations.

## Roadmap

| Day | Goal and scope | Required validation | Status |
|---|---|---|---|
| 01 | Local Windows Server, AD DS, DNS, OU structure, users, security groups | DNS, AD queries, DC health, evidence | Configuration and listed server-side tests completed |
| 02 | AD administration, GPO, password/lockout policy, firewall, screen lock, share mapping, local administrator management | In-scope vs out-of-scope GPO; gpupdate and gpresult | Recorded configuration and tests completed on 2026-10-01; see docs/day-02.md and tests/day-02.md |
| 03 | Separate admin identities, least privilege, Windows LAPS where supported, service account, auditing and group-change monitoring | Standard-user denial vs authorized administration; logs | Recorded scope completed on 2026-10-01; see docs/day-03.md and tests/day-03.md |
| 04 | Cloud Sync, UPN, password hash synchronization, pilot group scope, users and groups | In-scope synchronization; excluded user absent | Recorded scope completed on 2026-10-01; see docs/day-04.md and tests/day-04.md |
| 05 | Controlled hybrid identity troubleshooting | Problem → Investigation → Root Cause → Resolution → Verification | Recorded scope completed on 2026-10-02; see docs/day-05.md and tests/day-05.md |
| 06 | Windows 11, Entra Join, Intune enrollment, ownership and primary user | Device identity, join type and enrollment verification | Recorded scope completed on 2026-10-02; see docs/day-06.md and tests/day-06.md |
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
- The existing Baltic Finance Lab Entra tenant is connected through Cloud Sync. Four staff users use bfl.-prefixed UPNs under the tenant's onmicrosoft.com suffix to preserve existing cloud identities. The .test AD domain is unchanged.
- BFL-WKS01 remains AD joined for earlier exercises. Day 06 used a separate BFL-WKS02 VM, now Entra joined and managed by Intune, with Corporate ownership and Anna Finance as primary user. Device name was verified locally and in Intune after an additional restart.
- BFL-DC01 remains in the built-in Domain Controllers OU.
- adm.onprem belongs to Domain Users and GG-Workstation-Admins; elevated workstation operation passed while Anna was denied. It is excluded from cloud synchronization.
- Windows LAPS manages labadmin with encrypted AD backup; rotation and Anna's lack of read access were tested. gmsa-labtask resides in Service Accounts, is authorized for BFL-WKS01 and runs a manually triggered test task. The Cloud Sync agent uses a separate service identity.
- One NTP source and one DC are lab limitations. VMware host/guest time synchronization settings have not been inspected.
- The owner reported an existing Azure free subscription with USD 200 credit and Entra ID P2. No additional trial or Azure compute resource was created in the recorded steps; balances and expiration dates remain unverified.
- Cloud Sync is a pilot scoped to GG-Finance, GG-IT and GG-Security direct membership, replacing the original OU-scope plan. The old bfl.local configuration was disabled. BFL-DC01 hosts the sole active lab agent.

- Day 05 added tst.sync through GG-IT after a controlled scope failure. The test identity is retained in both directories with AD Enabled=False and cloud AccountEnabled=False; GG-IT membership remains. The pilot therefore contains four staff identities plus one disabled test identity.

- Day 06 uses the existing M365 E5 trial (approximately three weeks remaining, owner-reported on 2026-10-02). Anna's Intune license assignment and membership in BFL-Intune-Pilot-Users were confirmed; automatic MDM scope is Some for that group. Exact trial expiration remains unrecorded. The visible Compliant badge is not a custom-policy test.

## Daily completion standard

Document objective, context, configuration, security rationale, troubleshooting, result and lessons learned. Record test preconditions, procedure, expected/actual result, status and evidence. Never mark an unperformed test PASS.

## Next action

Begin Day 07 by inspecting existing Intune policies and assignments, then select justified configuration settings for the BFL-WKS02 pilot and verify endpoint results. Preserve earlier tenant exercises. Custom compliance and Conditional Access tests remain Day 08. Do not add empty folders for future work.
