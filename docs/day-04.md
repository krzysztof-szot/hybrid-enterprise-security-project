# Day 04 — Microsoft Entra Cloud Sync pilot

Date: 2026-10-01. Recorded synchronization scope and functional checks completed.

[Tests](../tests/day-04.md) · [Screenshots](../evidence/day-04/README.md) · [Text evidence and confirmations](../evidence/day-04/console-excerpts.md)

## Objective and existing tenant

Connect balticfinance.test to the existing Baltic Finance Lab Entra tenant while preserving unrelated cloud exercises. The owner reported an Azure free subscription with USD 200 credit and Entra ID P2. Credit balance, license assignments and trial expiration dates were not independently inspected. No additional trial or Azure compute resource was required for these recorded steps.

The tenant already contained cloud users, groups, applications and devices, including anna.finance and peter.finance. It was not an empty tenant. Public documentation uses <tenant>.onmicrosoft.com below; the actual suffix was supplied and configured by the owner. Tenant ID, public IP and unrelated identities are unnecessary to reproduce the design.

## Identity collision prevention

The owner selected separate project identities with a bfl. UPN prefix, preserving existing cloud-only Anna/Peter accounts.

| Local sAMAccountName | Prepared UPN |
|---|---|
| anna.finance | bfl.anna.finance@<tenant>.onmicrosoft.com |
| peter.finance | bfl.peter.finance@<tenant>.onmicrosoft.com |
| adam.it | bfl.adam.it@<tenant>.onmicrosoft.com |
| sara.security | bfl.sara.security@<tenant>.onmicrosoft.com |

Checks found empty mail and proxyAddresses for all four local users; the owner confirmed that the four new UPNs did not exist in Entra. The tenant suffix was added to the AD forest's alternate UPN suffixes and assigned to these users. sAMAccountName, object identity, department OUs and BFL\username logons were preserved. adm.onprem and tst.lockout were not changed.

No hard matching or manual creation of the four target cloud accounts was performed. UPN prefixing was a collision-avoidance decision for this tenant, not a general migration requirement.

## Old environment and cloud administration

The old bfl.local Cloud Sync configuration was quarantined, with an inactive BFL-DC01.bfl.local agent. The owner disabled the configuration and reported Connect Sync disabled. Quarantine was not treated as equivalent to disabled. Deletion of the old configuration/agent was not requested or verified.

A cloud-only bfl.hybrid.admin@<tenant>.onmicrosoft.com account was created with Hybrid Identity Administrator. Account creation, role assignment, login and MFA were owner-confirmed. Whether the role was assigned active or eligible through PIM was not recorded. The account is not synchronized from local AD.

## Agent installation

The agent was installed on BFL-DC01 to fit the local lab's resource budget. DC installation is supported, but this is a single-agent, single-DC deployment with no high availability.

Preflight output showed VaultSvc Running/Manual, effective PowerShell policy RemoteSigned and .NET Release=533509. The provisioning-agent installer was obtained through the Cloud Sync portal. Configuration used the cloud Hybrid Identity Administrator and local domain-administrator credentials, with Create gMSA for the agent service and domain balticfinance.test. The resulting agent appeared Active as BFL-DC01.balticfinance.test.

The wizard's agent service account is separate from Day 03 gmsa-labtask. Its actual AD object name and granted ACLs were not independently queried in the supplied evidence. Agent binary version and local service-status output were not captured.

### Installation troubleshooting

1. The embedded sign-in window was blocked by IE Enhanced Security Configuration for aadcdn.msftauth.net. The instructed workaround temporarily disabled IE ESC for Administrators only, then reopened the wizard.
2. A subsequent login failed because the username began fl.hybrid.admin rather than bfl.hybrid.admin. Correcting the missing initial b resolved the visible typo.
3. Registration later succeeded, as shown by the active-agent screenshot.

Restoring IE ESC for Administrators to On was requested after configuration, but explicit confirmation was not supplied. This remains a follow-up check, not a completed hardening claim.

## Pilot synchronization configuration

Created an AD → Microsoft Entra ID configuration for balticfinance.test with Password hash sync enabled. The pilot uses Selected security groups rather than the originally planned OU scope:

~~~text
CN=GG-Finance,OU=Groups,OU=BFL,DC=balticfinance,DC=test
CN=GG-IT,OU=Groups,OU=BFL,DC=balticfinance,DC=test
CN=GG-Security,OU=Groups,OU=BFL,DC=balticfinance,DC=test
~~~

Their direct members are Anna/Peter, Adam and Sara respectively. Nested membership is not used. This pilot-only group scope avoids moving existing objects and keeps workstation administrators, the disabled lockout account and service accounts outside the selected groups. Future group membership changes can change sync scope and must be reviewed.

### Positive and negative on-demand tests

Anna was provisioned by her DN:

~~~text
CN=Anna Finance,OU=Finance,OU=Users,OU=BFL,DC=balticfinance,DC=test
~~~

Import, scope and export succeeded. Export details explicitly reported creation of the new bfl.anna.finance account. On-demand provisioning was a real write, not a simulation.

For adm.onprem, the portal summary misleadingly displayed Object is in scope while the final action was skipped with JoinNotFound. That summary alone was not accepted as a passing exclusion test.

The imported GUID was resolved using Get-ADUser and matched CN=On-prem Admin,OU=Admins,OU=BFL,DC=balticfinance,DC=test. Detailed scoping showed Active in the source system=True and Scoping filter evaluation passed=False. The corrected screenshot preserves this detail. We do not assign a universal meaning to JoinNotFound; exclusion is supported by the detailed filter result plus the owner's later absence check.

## Enablement and outcome

The configuration was enabled after the tests. The owner reported all four new users present. Anna's screenshot shows On-premises sync enabled=Yes, and the owner confirmed successful cloud login using her current AD password.

Final checks were owner-confirmed without additional screenshots:

- GG-Finance, GG-IT and GG-Security are synchronized with the intended new project users.
- adm.onprem and tst.lockout were not created in Entra.
- Original cloud-only anna.finance and peter.finance still show On-premises sync enabled=No.
- The new configuration had the expected healthy state with no reported errors or unexpected results.

Only Anna's password-based cloud sign-in was tested. No claim is made for initial password synchronization/login of Peter or Adam, whose original first-logon password-change requirement had not been separately resolved in the supplied record.

## Limitations and follow-up

- Group-based scope is a pilot choice; revisit OU/attribute scope for broader deployment.
- The exact agent version, agent gMSA ACLs, deletion-protection threshold and notification configuration were not captured.
- No exported provisioning/sign-in logs, agent failover test, password-change propagation timing test or comprehensive matching audit.
- No Entra join, hybrid device join, Intune enrollment, password writeback, Conditional Access deployment or new cloud VM was performed in this stage.
- Existing tenant policies and exercises were not exhaustively inventoried. MFA completion for the cloud administrator is owner-confirmed; Anna's MFA outcome was not separately reported.

Next: Day 05 controlled hybrid-identity troubleshooting using the established pilot, with a reversible change and before/after evidence.

## References

- [Cloud Sync prerequisites](https://learn.microsoft.com/en-us/entra/identity/hybrid/cloud-sync/how-to-prerequisites)
- [Install the provisioning agent](https://learn.microsoft.com/en-us/entra/identity/hybrid/cloud-sync/how-to-install)
- [Configure scope and enable provisioning](https://learn.microsoft.com/en-us/entra/identity/hybrid/cloud-sync/how-to-configure)
- [Existing tenant matching considerations](https://learn.microsoft.com/en-us/entra/identity/hybrid/connect/how-to-connect-install-existing-tenant)
