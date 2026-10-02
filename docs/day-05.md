# Day 05 — Controlled Cloud Sync troubleshooting

**Session:** 2026-10-02  
**Status:** Recorded troubleshooting and account-disable propagation completed.

## Objective and context

Diagnose a deliberately excluded test identity, apply a limited correction, verify provisioning, and disable the test account after the exercise. The owner performed the lab actions; this document records supplied screenshots and console/portal output.

The existing Cloud Sync configuration for `balticfinance.test` uses the active agent on BFL-DC01 and selected security groups: GG-Finance, GG-IT and GG-Security. The pilot uses direct group membership. Its setup is documented in [Day 04](day-04.md).

| Item | Lab value |
|---|---|
| AD account | `tst.sync` |
| Display name | BFL Sync Test |
| Cloud UPN | `bfl.tst.sync@<tenant>.onmicrosoft.com` |
| AD location | `OU=IT,OU=Users,OU=BFL,DC=balticfinance,DC=test` |
| Department | IT |
| Description | Day 05 controlled synchronization troubleshooting |
| Corrective group membership | GG-IT |

The public UPN suffix is replaced with a placeholder. No password is recorded.

## Problem

The new test user was deliberately left outside the selected synchronization groups. Being located in the IT OU did not satisfy this configuration's group-based scope.

Provision on demand imported the object but skipped the final action. The [initial screenshot](../evidence/day-05/01-sync-user-out-of-scope.png) records:

- Active in the source system: **True**.
- Scoping filter evaluation passed: **False**.
- Final result: **Object was skipped**.

The generic workflow caption also says “Object is in scope.” That caption conflicts with the detailed evaluation. The diagnosis uses the detailed False value and skipped action. The screenshot itself does not display the identity; its association with tst.sync comes from the exercise context.

## Investigation and root cause

The relevant configuration was the existing selected-group filter. The intended initial condition was missing membership in the scoped groups; the detailed evaluation confirmed exclusion. This was a controlled scope issue, with successful source import, rather than evidence of a broken agent.

No initial membership command output was retained. The later supplied membership query confirms that the corrected account belongs to Domain Users and GG-IT.

## Resolution

The correction added only the test account to the existing IT group:

```powershell
Get-ADPrincipalGroupMembership "tst.sync" | Select-Object Name

Add-ADGroupMember -Identity "GG-IT" -Members "tst.sync" -ErrorAction Stop

Get-ADPrincipalGroupMembership "tst.sync" | Select-Object Name
```

Provision on demand was repeated for:

```text
CN=BFL Sync Test,OU=IT,OU=Users,OU=BFL,DC=balticfinance,DC=test
```

The filter was not broadened to all objects. Existing pilot identities and the exclusion of administrative identities remained the design constraints.

## Verification

The [post-fix export](../evidence/day-05/02-sync-user-after-scope-fix.png) reports that the test user was created in Microsoft Entra ID. It shows AccountEnabled=True, the expected display name, IT department, description and source DNS domain.

The [provisioning log](../evidence/day-05/03-sync-user-provisioning-log.png) independently records BFL Sync Test with action **Create**, source **Active Directory**, target **Microsoft Entra ID**, and status **Success**. Its displayed timestamp is **2026-10-02 06:31:32**, with the portal set to show local dates; no separate timezone conversion is asserted.

## Cleanup and disable propagation

The account was retained in GG-IT so that it stayed within synchronization scope. The owner disabled it in AD:

```powershell
Disable-ADAccount -Identity "tst.sync" -ErrorAction Stop
Get-ADUser "tst.sync" | Select-Object SamAccountName, Enabled
```

Actual output showed `tst.sync False`. After provisioning, the supplied portal export text reported an update to the same cloud UPN with **AccountEnabled=False**. These are [transcribed text evidence](../evidence/day-05/console-excerpts.md), not an additional screenshot.

Final recorded state: the test identity remains in both directories, disabled, with GG-IT membership retained. This adds one disabled test identity to the four staff identities established in Day 04.

## Results and limitations

The exercise demonstrates scope diagnosis, correction through group membership, successful object creation and propagation of the disabled state. Detailed evaluation and target export values were more useful than generic green workflow captions.

These checks used provisioning on demand. Scheduled-cycle latency, test-user password synchronization, denied sign-in after disabling, existing-session revocation and out-of-scope deletion were not tested. AccountEnabled=False alone does not prove those separate outcomes.

See the [test record](../tests/day-05.md) and [evidence inventory](../evidence/day-05/README.md).

## Next stage

Day 06 covers endpoint identity and Intune enrollment. First establish the endpoint plan and licensing prerequisites: BFL-WKS01 is already AD joined and supports earlier exercises, so its join state must not be silently replaced.
