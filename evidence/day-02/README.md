# Day 02 — Evidence

Ten screenshots uploaded by the owner and reviewed during the lab session. Each image supports only the claim stated below.

[Implementation](../../docs/day-02.md) · [Test record](../../tests/day-02.md) · [Console excerpts and observations](console-excerpts.md)

## 01 — Domain join

BFL-WKS01 belongs to balticfinance.test; PartOfDomain and the secure-channel test return True.

![Domain join](01-bfl-wks01-domain-join.png)

## 02 — Domain user session

Anna's identity, BFL-DC01 logon server and GG-Finance membership are visible. The token has medium integrity.

![Domain user session](02-anna-domain-logon.png)

## 03 — Password and lockout policy

Shows minimum length 14, history 24, complexity enabled, threshold 10, 15-minute lockout/window, and reversible encryption disabled. This image records configuration.

![Password and lockout policy](03-domain-password-lockout-policy.png)

## 04 — GPO in scope

GPO-BFL-Workstation-Security appears under Applied Group Policy Objects. The screenshot cuts off before the inactivity and firewall results; those are preserved as text evidence.

![GPO in scope](04-workstation-gpo-applied.png)

## 05 — GPO outside scope

After the temporary move to the parent Computers OU, only Default Domain Policy is listed. This proves scope exclusion, not complete reversal of persistent settings.

![GPO outside scope](05-workstation-gpo-out-of-scope.png)

## 06 — Account lockout event

Security event 4740 names tst.lockout and caller BFL-WKS01 at 2026-10-01 09:26:11. The owner separately confirmed ten deliberate incorrect attempts; the image alone does not count attempts.

![Account lockout event](06-account-lockout-event4740.png)

## 07 — Finance access allowed

Anna creates and reads anna-access-test.txt through the Finance UNC path.

![Finance access allowed](07-finance-access-allowed.png)

## 08 — Finance access denied

Sara's identity and Access is denied when listing Finance demonstrate the negative authorization test.

![Finance access denied](08-finance-access-denied.png)

## 09 — Finance drive mapped

F: points to the expected Finance UNC path with Status OK; the test file is readable. This was captured in Anna's session, but whoami is not visible in this image.

![Finance drive mapped](09-finance-drive-mapped.png)

## 10 — Managed local administrator group

GG-Workstation-Admins is present alongside Domain Admins and the two original local accounts after policy refresh. The domain group is empty; this is not proof of an individual user's administrative capability.

![Managed local administrator group](10-workstation-local-admins.png)

## Evidence without additional screenshots

The text record covers firewall and inactivity settings, final computer GPO application, Sara's absent F: mapping, the disabled test account, and share configuration. The 15-minute lock/password requirement and restoration of power timers are owner observations. No EVTX export, packet capture or repaired XML snapshot was supplied. Troubleshooting screenshots were used diagnostically and are not required as extra portfolio evidence.
