# Day 09 — Endpoint security test record

**Executed by:** project owner, 2026-10-02, on BFL-WKS02.
**Documented from:** supplied configuration summaries, owner confirmations and the [16 published screenshots](../evidence/day-09/README.md).
**Scope:** one Entra-joined, Intune-managed pilot device; Anna Finance; existing Day 07/08 controls.

PASS below applies only to the stated observation. It does not certify all settings in a policy. Evidence numbers link to the screenshot inventory.

| ID | Preconditions and procedure | Expected result | Actual result and status | Evidence |
|---|---|---|---|---|
| AV-01 | Assign the Windows AV profile to the pilot, synchronize and inspect the device report | Policy applies without reported error | **PASS:** 1 Succeeded, BFL-WKS02 Success, 0 Error/Conflict in published image | [01](../evidence/day-09/01-defender-av-policy-status.png) |
| AV-02 | With protection On, run a quick scan and inspect its result | Scan completes and reports findings | **PASS:** 10,852 files, 1m31s, 0 threats; intelligence up to date | [02](../evidence/day-09/02-defender-quick-scan-result.png) |
| AV-03 | After policy application, inspect real-time and cloud-delivered protection | Both remain enabled and centrally managed | **PASS, owner-confirmed:** both On; controls could not be changed | [Session record](../evidence/day-09/console-excerpts.md) |
| AV-04 | Perform the EICAR file exercise and inspect Protection history | Test threat detected and handled | **PASS for observed detection/quarantine:** owner reported Threat found; screenshot shows Threat quarantined, Severe. Exact identity/path **not independently verified** because the entry stayed collapsed | [03](../evidence/day-09/03-defender-eicar-detection.png) |
| FW-01 | Apply firewall profile, synchronize and inspect report plus local profiles | Successful application; all profiles On | **PASS:** 1 Succeeded; Domain/Private/Public On, Public active | [04](../evidence/day-09/04-firewall-policy-status.png), [05](../evidence/day-09/05-firewall-device-status.png) |
| FW-02 | With Public active, enable the scoped Edge outbound block and load Bing | Edge connection denied | **PASS:** ERR_NETWORK_ACCESS_DENIED | [06](../evidence/day-09/06-firewall-edge-blocked.png) |
| FW-03 | Disable the temporary rule, synchronize and reload the site; open Outlook Web | Browser access restored | **PASS:** Bing loads; owner confirmed Outlook works and rule Enabled=Disabled | [07](../evidence/day-09/07-firewall-access-restored.png), owner confirmation |
| BL-01 | On already encrypted C:, apply BitLocker policy; inspect report and drive | Successful policy delivery; encryption remains On | **PASS for delivery/state preservation:** 1 Succeeded; C: On owner-confirmed. Initial encryption was not tested | [08](../evidence/day-09/08-bitlocker-policy-status.png) |
| ASR-01 | Assign six Audit rules and synchronize | Audit policy delivery succeeds | **PASS:** 1 Succeeded, no displayed errors/conflicts | [09](../evidence/day-09/09-asr-audit-policy-status.png) |
| ASR-02 | Run the obfuscated-script demonstration from Downloads in Audit; inspect Defender/Operational | Audited operation recorded without enforcement | **PASS:** event 1122, matching rule/script/wscript.exe; owner observed blank Notepad | [10](../evidence/day-09/10-asr-audit-event.png) |
| ASR-03 | Change only the obfuscated-script rule to Block, synchronize and repeat | Operation blocked and corresponding event recorded | **PASS:** event 1121 for the same rule and script | [11](../evidence/day-09/11-asr-block-event.png) |
| MDE-01 | Existing MDE license enabled; enable Intune connector, deploy Auto from connector onboarding, inspect both portals | Connector available, policy delivered, device onboarded and reporting | **PASS:** connector Available owner-confirmed; 1 Succeeded; device Onboarded, Active, Full | [12](../evidence/day-09/12-mde-onboarding-policy-status.png), [13](../evidence/day-09/13-mde-device-onboarded.png) |
| TP-01 | Apply Tamper Protection profile, synchronize and inspect endpoint UI | Protection On and managed | **PASS:** 1 Succeeded; On, greyed out, managed by administrator | [14](../evidence/day-09/14-tamper-protection-enabled.png), [15](../evidence/day-09/15-tamper-protection-device-status.png) |
| BL-02 | Recovery key available and C: encrypted; initiate remote key rotation, inspect action and key entry, check C: | Rotation completes, new key identifier, encryption remains On | **PASS:** action Complete; new Key Id and C: On owner-confirmed | [16](../evidence/day-09/16-bitlocker-key-rotation-status.png) |
| REG-01 | Following ASR and firewall tests, check normal browsing, Outlook, test-rule state, compliance and CA | Access works with security controls retained | **PASS, owner-confirmed:** Edge/Outlook work, test rule Disabled, device Compliant, CA On. Later image 16 again shows Compliant; no separate post-optional browser/CA retest recorded | [Session record](../evidence/day-09/console-excerpts.md), [16](../evidence/day-09/16-bitlocker-key-rotation-status.png) |

## Not verified or not performed

| Item | Status and reason |
|---|---|
| Final firewall log settings and generated ALLOW/DROP records | **Not verified:** no final configuration export or log file supplied |
| Expanded EICAR name and path | **Not verified:** history entry could not be expanded |
| Scheduled quick scan, four-hour update cadence and PUA blocking | **Not tested:** configured values are not runtime tests |
| Other five selected ASR rules | **Not tested:** only obfuscated scripts exercised; no desktop Word/Excel |
| MDE test alert, advanced hunting, incident response | **Not tested:** onboarding/health only |
| BitLocker initial encryption, boot recovery and automatic rotation after key use | **Not tested:** drive already encrypted; manual rotation tested |
| Tamper bypass or attempted malicious change | **Not tested:** managed On state verified |
| Demonstration .js cleanup | **Not confirmed:** cleanup requested; no completion confirmation supplied |
| Emergency account and broad production rollout | **Not tested / outside pilot scope** |

See [implementation and troubleshooting](../docs/day-09.md) for the unresolved sync-error cause, demonstration authentication issue and configuration details.
