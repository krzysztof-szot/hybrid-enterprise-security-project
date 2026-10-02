# Day 10 — Azure RBAC and Blob data access

**Session:** 2026-10-02. **Status:** recorded functional scope completed; temporary lab resources cleaned up.

[Tests](../tests/day-10.md) · [13 screenshots](../evidence/day-10/README.md) · [Session excerpts](../evidence/day-10/console-excerpts.md)

## Objective and context

Compare Reader, Contributor, Security Reader and Storage Blob Data Reader through allowed and denied operations. Demonstrate that access to resource configuration does not automatically grant access to blob contents, then verify access removal.

The owner performed all Azure actions manually. Tenant Bootstrap Administrator was the administrative account; its subscription-level Owner role was owner-confirmed. Anna Finance was the test identity, using Azure Portal on BFL-WKS02. These tests concern Azure resource roles; no Microsoft Entra directory role was assigned to Anna for this exercise.

| Item | Recorded value |
|---|---|
| Directory | Baltic Finance Lab |
| Subscription | Azure subscription 1; Active, owner-confirmed |
| Credit at session start | EUR 159.53 remaining; expires 2026-10-13, owner-confirmed |
| Offer type | Not identified |
| Temporary resource group | rg-bfl-rbac-lab |
| Region | Poland Central |
| Storage account | stbflrbaclab01 |
| Container | rbac-test |
| Test blob | rbac-test.txt |

Resource-group tags were Project=BalticFinance, Environment=Lab and Purpose=Day10-RBAC. A separate RbacTest tag was used for the write tests. Existing resources visible in Defender for Cloud were not created or remediated as part of Day 10.

## Role scope and sequence

| Identity / role | Scope | Purpose and lifecycle |
|---|---|---|
| Anna / Reader | rg-bfl-rbac-lab | View resource configuration; retained during later tests, scope removed with lab cleanup |
| Anna / Contributor | rg-bfl-rbac-lab | Temporary tag-write comparison; removed before Security Reader testing |
| Anna / Security Reader | Azure subscription 1 | Read subscription security information; removal explicitly confirmed at cleanup |
| Administrator / Storage Blob Data Contributor | stbflrbaclab01 | Prepare container and upload test data; account later deleted with the lab group |
| Anna / Storage Blob Data Reader | rbac-test container | Read this container's data; removed and denial re-tested before cleanup |

Reader was first created as Eligible because the guidance skipped the assignment-type step. It was corrected to Active before functional testing. The published screenshot 01 shows the corrected current assignment.

Security Reader was initially assigned at the resource-group scope. This was corrected to the subscription scope before the Defender for Cloud test. Screenshot 06 shows “This resource” on the **subscription** page, which is the intended scope.

The temporary Reader and Contributor assignments appear as active permanent assignments in screenshot 05. Removal was therefore an explicit cleanup step rather than reliance on automatic expiry.

## Management-plane tests

### Reader: view allowed, tag write denied

With Reader active on the group, Anna opened its Overview. An attempt to add RbacTest=ReaderAttempt failed with **AuthorizationFailed**. This supplies both a positive read observation and a real rejected save, rather than relying only on a role description.

### Contributor: tag write allowed, role-assignment UI unavailable

Contributor was added at the same group scope while Reader remained. Anna saved **RbacTest=ContributorAllowed**; screenshot 04 shows the resulting tag.

In Access control (IAM), **Add role assignment** and **Add custom role** were disabled. Screenshot 05 also shows Anna's two active roles and their scope. This validates the portal limitation; no direct role-assignment API request was executed.

Contributor was then removed to avoid carrying write access into subsequent read-only tests.

### Security Reader: security information visible, resource write denied

Anna received active Security Reader at subscription scope and retained Reader on the lab group. She opened **Microsoft Defender for Cloud → Recommendations**, filtered to Azure subscription 1. Recommendations for existing resources were visible.

Visible rows were **Not evaluated**. The displayed zero Critical count is not evidence of an issue-free subscription, completed assessment or remediation. This was a read-access exercise, not the Day 13 posture-remediation milestone.

Anna then tried RbacTest=SecurityReaderAttempt on the lab group; saving failed with **AuthorizationFailed**. This tests resource-tag write denial with the current role combination. It is not a test of changing Defender for Cloud settings.

## Storage setup and evidence boundary

The owner created stbflrbaclab01 and confirmed the administrator's Storage Blob Data Contributor assignment. The deployment name with a numeric suffix was distinguished from the actual storage-account name.

The guided setup specified the following. A final Review + create summary or exported account configuration was **not supplied**, so the table records the selected setup instructions, not an independently verified configuration audit.

| Area | Guided setting |
|---|---|
| Basics | Standard, locally redundant storage (LRS), Poland Central, Blob Storage, Hot |
| Authentication | Storage-account key access disabled; default portal authorization Microsoft Entra |
| Transport / anonymous access | Secure transfer enabled; TLS 1.2 minimum; anonymous blob access disabled |
| Blob features | Hierarchical namespace, SFTP, NFS v3 and cross-tenant replication disabled |
| Networking | Public network access from all networks; Microsoft network routing; no private endpoint |
| Recovery | Blob and container soft delete: 7 days; classic file-share soft delete: 7 days if available |
| Additional history | Point-in-time restore, versioning, change feed and version-level immutability disabled |
| Encryption | Microsoft-managed keys; infrastructure encryption disabled |
| Tags | Project=BalticFinance, Environment=Lab, Purpose=Day10-RBAC |

The public endpoint was selected to isolate identity authorization from network restrictions for this short exercise. This was not a network-isolation test. Costs for stored data and operations were discussed; only a small text file was used, and the temporary account was subsequently cleaned up. Actual charges were not measured.

The container was instructed to use **Private (no anonymous access)**. The administrator explicitly confirmed **Microsoft Entra user account** authentication and successful upload. Screenshots 09–12 independently show that same authentication method during Anna's tests. No account keys or SAS tokens were used in the recorded test flow.

## Data-plane test: deny → allow read → deny write → revoke

The administrator uploaded rbac-test.txt containing:

```text
Baltic Finance - Day 10 RBAC test.
```

1. **Before data permission:** Anna could navigate to the storage account/container, but listing blobs was rejected: “You do not have permissions to list the data using your user account with Microsoft Entra ID.” The empty list was an authorization outcome, not an empty container.
2. **After container-scoped Storage Blob Data Reader:** Anna listed and downloaded rbac-test.txt. Screenshot 10 shows the blob, Microsoft Entra authentication and the browser download. The owner separately confirmed the downloaded text matched the line above.
3. **Write attempt:** Anna tried to upload anna-upload-test.txt. The portal rejected the upload as unauthorized for the permission in use. The original blob remained listed.
4. **After removing the data role:** the administrator removed Anna's Storage Blob Data Reader assignment. The repeated list request was rejected again, with Microsoft Entra authentication still selected.

The test demonstrates a real difference between management-plane Reader and a data-plane reader role. Revocation was verified for a new online request; it does not revoke a previously downloaded local file. Exact role-propagation time was not measured.

## Troubleshooting and lessons

| Observation | Correction or interpretation |
|---|---|
| Reader appeared under Eligible assignments | Guidance had omitted Assignment type; corrected to Active and verified Current role assignments before testing |
| Security Reader displayed This resource on the group page | Assigned at the wrong scope; corrected to the subscription and verified there |
| Deployment name contained a numeric suffix | Used Go to resource and confirmed actual account name stbflrbaclab01 |
| Authentication selector was hard to locate | Opened the container and inspected Authentication method above the blob list |
| Container showed 0 items with an authorization banner | Treated as denied listing, not missing test data |
| Role descriptions could suggest success without a test | Used actual tag-save errors, blob upload denial and post-revocation denial; labelled the disabled IAM button as UI evidence |

Scope, active assignment state and the authorization method all matter. Testing with the user identity exposed limitations that an administrator session would not demonstrate.

## Cleanup and final state

After the revocation test, the owner explicitly confirmed removal of Anna's **subscription-level Security Reader**. The instructed cleanup removed rg-bfl-rbac-lab and its temporary Storage account, container and test blob. Screenshot 13 shows the group absent from the refreshed resource-group list, with NetworkWatcherRG and rg-bfl-identity-lab still listed.

The screenshot is a final inventory view, not an exported deletion operation log or a separate post-delete role inventory. Local downloaded test files were not confirmed deleted. Existing resources outside the temporary group were outside this cleanup.

## Limitations and next milestone

Evidence consists of the 13 screenshots and owner confirmations. No automated retest, exported Activity Log, storage diagnostic log, final settings export, billing reconciliation, direct role-assignment API denial, Defender-setting write test or cross-container isolation test was collected. The positive Blob read result includes an owner confirmation of file contents.

Next is **Day 11: Key Vault, managed identity and access control**. Inspect existing suitable resources and their costs first. Plan an authorized secret read and a denied identity, using a non-sensitive test secret and no hard-coded credentials. Recheck credits and the 2026-10-13 expiry before provisioning.
