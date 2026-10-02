# Day 10 — Azure RBAC test record

**Executed by:** project owner, 2026-10-02. Administration: Tenant Bootstrap Administrator. Test user: Anna Finance on BFL-WKS02.
**Evidence:** [13 screenshots](../evidence/day-10/README.md) and [owner/session confirmations](../evidence/day-10/console-excerpts.md).
**Status:** recorded tests completed; temporary resources cleaned up. No new tests were run during documentation.

PASS is limited to the described operation and evidence. Earlier setup mistakes were corrected before the relevant functional test.

| ID | Preconditions and procedure | Expected result | Actual result / status | Evidence |
|---|---|---|---|---|
| R-01 | Active Reader on rg-bfl-rbac-lab; open Overview as Anna | Group details readable | **PASS:** group Overview visible | [01](../evidence/day-10/01-rbac-reader-assignment.png), [02](../evidence/day-10/02-rbac-reader-view.png) |
| R-02 | Reader only at lab-group scope; save RbacTest=ReaderAttempt | Write rejected | **PASS:** AuthorizationFailed | [03](../evidence/day-10/03-rbac-reader-write-denied.png) |
| C-01 | Add active Contributor on same group; save RbacTest=ContributorAllowed | Tag write succeeds | **PASS:** resulting tag visible | [04](../evidence/day-10/04-rbac-contributor-write-success.png), [05](../evidence/day-10/05-rbac-contributor-role-assignment-denied.png) |
| C-02 | Reader and Contributor active; inspect IAM Add menu as Anna | Role-assignment option unavailable | **PASS, UI only:** Add role assignment disabled; no direct API attempt | [05](../evidence/day-10/05-rbac-contributor-role-assignment-denied.png) |
| S-01 | Contributor removed; active Security Reader on subscription; open Defender for Cloud Recommendations | Security view accessible | **PASS:** subscription recommendations visible; Not evaluated is not a posture pass | [06](../evidence/day-10/06-rbac-security-reader-assignment.png), [07](../evidence/day-10/07-rbac-security-reader-view.png) |
| S-02 | Reader on group plus Security Reader on subscription; save RbacTest=SecurityReaderAttempt | Resource write rejected | **PASS:** AuthorizationFailed; not a Defender-settings write test | [08](../evidence/day-10/08-rbac-security-reader-write-denied.png) |
| B-01 | Test blob already uploaded; Anna has no blob data role; open container using Entra authentication | Data listing denied | **PASS:** explicit Entra permission error | [09](../evidence/day-10/09-rbac-blob-read-denied.png) |
| B-02 | Assign Anna Storage Blob Data Reader on rbac-test; list, download and open test file | File readable | **PASS:** listed/downloaded; exact contents owner-confirmed | [10](../evidence/day-10/10-rbac-blob-read-success.png), owner confirmation |
| B-03 | Same read-only data role; upload anna-upload-test.txt through Entra authentication | Upload rejected | **PASS:** failed upload, operation not authorized for permission | [11](../evidence/day-10/11-rbac-blob-write-denied.png) |
| B-04 | Remove Anna's data-reader role; reopen container and list through Entra authentication | Online data access denied again | **PASS:** explicit listing denial; existing local download unaffected | [12](../evidence/day-10/12-rbac-blob-access-revoked.png) |
| CL-01 | Tests complete; remove Anna's Security Reader and delete isolated lab group | Temporary scope removed | **PASS for recorded cleanup:** Security Reader removal owner-confirmed; refreshed group list excludes lab group | [13](../evidence/day-10/13-rbac-lab-cleanup.png), owner confirmation |

## Setup validation and corrections

- Subscription Active and administrator Owner at subscription scope were owner-confirmed.
- Reader initially Eligible was changed to Active; screenshot 01 is the corrected evidence.
- Security Reader initially on the resource group was corrected to subscription scope; screenshot 06 is the corrected evidence.
- Administrator confirmed Storage Blob Data Contributor on stbflrbaclab01, Microsoft Entra authentication and successful upload.
- Container-scoped assignment of Anna's data role follows the recorded procedure; there is no separate screenshot exporting that assignment. Its functional effect is demonstrated by the before/after tests.
- Resource-group creation and tag setup were owner-confirmed and supported by the Overview/tag screenshots.

## Evidence limits

| Item | Status |
|---|---|
| Final account settings: LRS, key access disabled, anonymous access disabled, networking, retention and encryption | **Not independently verified:** guided settings; no final summary/export supplied |
| Authentication used in blob tests | **Verified:** Microsoft Entra user account visible in screenshots 09–12 |
| Direct role-assignment API rejection | **Not tested:** portal control was disabled |
| Defender for Cloud configuration write rejection | **Not tested:** negative test concerned a resource-group tag |
| Cross-container access isolation | **Not tested:** one container used |
| Blob delete/overwrite denial | **Not tested:** a new-file upload was rejected |
| Exact propagation delay and storage charges | **Not measured** |
| Full post-delete assignment inventory and deletion operation log | **Not supplied:** group absence and owner confirmation recorded |
| Cleanup of downloaded local test copies | **Not confirmed** |
| Existing recommendation remediation and paid-plan status | **Not assessed in this milestone** |

[Implementation, rationale and troubleshooting](../docs/day-10.md)
