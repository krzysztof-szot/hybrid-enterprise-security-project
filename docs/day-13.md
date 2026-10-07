# Day 13 — Defender for Cloud and Storage network access

Date: 2026-10-05  
Follow-up recorded: 2026-10-07  
Status: functional tests completed; selected recommendation recorded as Completed; post-reassessment Secure Score captured.

## Objective and scope

Follow Finding → Risk → Recommendation → Remediation → Verification for one existing Storage account. Restrict its network access while preserving authenticated Blob reads from an approved Azure subnet.

The owner explicitly authorized changing the existing `bflmi13a7k29` account. The exercise reused the Day 12 client VM and network. Azure configuration and tests were performed manually by the owner; this document records the supplied evidence, not a separate automated execution.

[Tests](../tests/day-13.md) · [Evidence inventory](../evidence/day-13/README.md) · [Actual output excerpts](../evidence/day-13/console-excerpts.md)

## Baseline and finding selection

- Defender CSPM was On, with Partial monitoring coverage. Foundational CSPM showed Full coverage. Visible workload-protection plans were Off; enabling Defender CSPM does not demonstrate that those plans were enabled.
- The owner accepted paid Defender CSPM use. Actual charges were not measured.
- The saved Secure Score baseline was **34%**, with **14/32 active secure score recommendations** and zero displayed attack paths. An earlier view showed 32%; that change preceded the Storage remediation and is not attributed to it.
- The selected recommendation was **Storage accounts should restrict network access using virtual network rules**, severity Medium, with one affected account.
- The account initially allowed public network access from all networks.
- Recommendations referring to BFL-CON01 and BFL-EJ01 were not selected: the owner reported deleting those older VMs for licensing reasons. Their lingering findings are not evidence of an NSG remediation.
- `Not evaluated` in the Risk level column and `Unassigned` in the Status column do not establish that the resource is compliant. The latter is a governance assignment status.

## Finding → risk → remediation

| Stage | Recorded interpretation or action |
|---|---|
| Finding | The existing Storage account allowed connections from all networks and appeared in the VNet-rules recommendation. |
| Risk | Broad network reachability leaves data access dependent on authorization without a narrow network boundary. This does not mean the blobs were anonymous or publicly readable. |
| Recommendation | Use selected networks, add an approved virtual network/subnet and remove public IP allow rules, as described in the recommendation details. |
| Remediation | Allow snet-client through a Storage service endpoint, remove the temporary workstation IP rule, and grant the client VM's managed identity container-scoped Blob read access. |
| Verification | Managed-identity Blob GET returned HTTP 200 before and after IP-rule removal. The workstation subsequently received HTTP 403. Final networking shows one VNet and no IPv4 allow rules. The later Defender view shows this account with status Completed (screenshot 15). |

## Resources and final recorded configuration

| Component | Value |
|---|---|
| Storage account | `bflmi13a7k29` |
| Container | `identity-lab` |
| Existing test blob | `bfl-identity-proof.txt`, 205 bytes |
| Network resource group | `rg-bfl-network-lab` |
| Virtual network | `vnet-bfl-day12`, `10.120.0.0/16` |
| Allowed subnet | `snet-client`, `10.120.1.0/24` |
| Service endpoint | Microsoft.Storage; Enabled shown for snet-client |
| Test VM | `vm-bfl-day12-client`, private IP `10.120.1.4` |
| VM identity | System-assigned managed identity used for the test |
| Data role | Storage Blob Data Reader |
| Assignment scope | `identity-lab` container, displayed as This resource |
| Public network access | Enabled from selected networks |
| Public IPv4 allow rules | None in final configuration |
| Exceptions | One retained exception: trusted Microsoft services |
| Network security perimeter | None associated |
| Client final power state | Stopped (deallocated), owner-confirmed |

The endpoint remains the Storage public service endpoint with network restrictions. A service endpoint does not create a Private Endpoint or a private IP for the Storage account. The separate private-link recommendation was not remediated in this exercise. The trusted-services exception remains part of the configuration, so the result must not be described as exclusive access by this VM.

## Implementation sequence

1. Captured Defender plans, recommendations and the Secure Score baseline.
2. Recorded the account's all-networks setting.
3. Changed to selected networks and temporarily allowed the workstation's public IPv4 address. Storage browser, using Access key authentication, still listed the existing 205-byte blob.
4. Reviewed the exact recommendation. IP allowlisting alone was insufficient for this finding: its remediation called for virtual network rules without allowed public IP ranges.
5. Added `vnet-bfl-day12 / snet-client` and enabled the Microsoft.Storage service endpoint.
6. Started the existing client VM, enabled its system-assigned managed identity and assigned Storage Blob Data Reader at container scope.
7. Ran the Python test below through VM Run command → RunShellScript. It obtained a token and read the existing blob: HTTP 200, 205 bytes.
8. Removed the workstation's public IP allow rule and saved the account's network settings.
9. Repeated the same VM test: HTTP 200, 205 bytes, PASS.
10. Retested access from the workstation in the portal. The container view returned HTTP 403 with a message indicating Storage networking might block public access.
11. Captured final networking and the still-listed Defender recommendation.
12. Stopped the client VM. The owner confirmed Stopped (deallocated). The server VM was not needed for Day 13; its last recorded state remains deallocated from Day 12.

## Reusable read test

Run inside the client VM through RunShellScript. Python 3 was already present. The script uses the VM's managed identity and does not require a Storage key, SAS, Azure CLI installation or an interactive login. It prints neither the access token nor blob contents.

Preconditions: running VM, enabled system-assigned identity, effective container-scoped role assignment, approved subnet rule and enabled service endpoint.

```bash
python3 - <<'PY'
import json
import sys
import urllib.request
import urllib.error
from email.utils import formatdate

opener = urllib.request.build_opener(
    urllib.request.ProxyHandler({})
)

stage = "Managed identity token"
try:
    token_url = (
        "http://169.254.169.254/metadata/identity/oauth2/token"
        "?api-version=2018-02-01"
        "&resource=https%3A%2F%2Fstorage.azure.com%2F"
    )
    request = urllib.request.Request(
        token_url, headers={"Metadata": "true"}
    )
    with opener.open(request, timeout=20) as response:
        token = json.load(response)["access_token"]

    print("Managed identity token: acquired")

    stage = "Blob read"
    blob_url = (
        "https://bflmi13a7k29.blob.core.windows.net/"
        "identity-lab/bfl-identity-proof.txt"
    )
    request = urllib.request.Request(blob_url, headers={
        "Authorization": "Bearer " + token,
        "x-ms-version": "2023-11-03",
        "x-ms-date": formatdate(usegmt=True),
    })

    with opener.open(request, timeout=30) as response:
        status = response.status
        size = len(response.read())

    print(f"HTTP status: {status}")
    print(f"Bytes read: {size}")
    if status != 200:
        raise RuntimeError("Unexpected HTTP status")
    print("PASS: blob read using VM managed identity.")

except urllib.error.HTTPError as error:
    print(f"FAIL: {stage}: HTTP {error.code}")
    print("Storage error code:",
          error.headers.get("x-ms-error-code", "not provided"))
    sys.exit(1)
except Exception as error:
    print(f"FAIL: {stage}: {type(error).__name__}")
    sys.exit(1)
PY
```

Both supplied VM results show `HTTP status: 200`, `Bytes read: 205` and the explicit PASS line. The Run Command wrapper's `Enable succeeded` message alone is not the test criterion.

## Troubleshooting and interpretation

| Observation | Resolution or interpretation |
|---|---|
| Recommendations were initially difficult to locate | Reviewed the subscription context and existing Owner access; used classic view to capture Secure Score. No additional human role grant is claimed. |
| Selected networks with a workstation IP rule still left the recommendation relevant | Read the recommendation's exact remediation; added the subnet/service endpoint and removed the IP rule. The intermediate state in screenshot 05 is not the final configuration. |
| Successful portal listing used Access key | Recorded it as a workstation access baseline, not an Entra RBAC test. VM reads separately verified managed-identity authentication. |
| Portal returned 403 after IP removal | Consistent with the intended network restriction when combined with the final settings and successful VM read. The error alone does not uniquely diagnose every possible authorization failure. |
| Defender still listed the account after tests | Preserved the initial pending result in screenshot 14. The later screenshot 15 shows the account with status Completed. Elapsed time or a refresh alone was not treated as proof. |

## Results and limitations

Functional verification is complete for the recorded read path and workstation denial. The selected Storage recommendation now displays Completed for bflmi13a7k29. The score captured on 2026-10-07 is 77%, up from the saved 34% baseline (+43 percentage points). The owner attributes the increase mainly to removal of two older, no-longer-needed VMs for licensing reasons. The screenshots establish the score change, not the numerical contribution of each change; the increase is not attributed solely to Storage hardening.

## Reassessment follow-up

[15 — Completed recommendation](../evidence/day-13/15-storage-recommendation-completed.png) shows the exact account with Status **Completed** and the top affected-resource counter at **0**. The filtered table includes one completed resource. Risk level remains **Not evaluated**, which is a risk-prioritization field. This records the Defender recommendation status; no separate Azure Policy Compliant result or raw Healthy assessment export was collected.

[16 — Secure Score after reassessment](../evidence/day-13/16-secure-score-after-reassessment.png), supplied on 2026-10-07, shows:

| Observation | Saved baseline (03) | Follow-up (16) |
|---|---|---|
| Secure Score | 34% | 77% |
| Active secure score recommendations | 14/32 | 9/32 |
| Displayed attack paths | 0 | 0 |

An intermediate session view showed 34% and 12/32; it was not uploaded as a separate numbered evidence file. The final view also shows resource health: Unhealthy 4, Healthy 2, Not applicable 5. Zero displayed attack paths does not establish an absence of all risk.

The owner confirmed that two older VMs had been deleted because they were no longer needed for licensing reasons. Their management-port findings remained visible before reassessment. Removing resources changes the assessed population; it is not evidence that their ports were secured or their operating systems patched. These deleted machines are separate from the Day 12 client/server VMs recorded as deallocated. No per-resource score export or fresh deletion-log audit was collected.

The portal denial screenshot does not show its authentication selector; Access key was used for the earlier baseline and retaining that method was instructed. No separate browser network trace was captured. The VM test proves a Blob GET succeeded; it does not prove write denial or absence of other inherited role assignments. No packet capture, Private Endpoint deployment, broad all-network negative test or full Azure configuration export was performed.

The client role assignment and network changes remain in place. Client deallocation is owner-confirmed rather than screenshot-backed. Deallocation is not deletion, and retained resources or enabled Defender plans can continue to incur costs.

## Follow-up

The Day 13 recommendation-status and score follow-up is now documented. Retain screenshots 03 and 14 as historical baseline/pending evidence alongside 15 and 16. The [final security assessment](final-security-assessment.md) now consolidates the remaining findings and recorded limitations; Days 14 and 15 are documented separately as completed exercises. No further Azure changes were performed as part of this documentation update.

## References

- [Azure Storage virtual network rules](https://learn.microsoft.com/en-us/azure/storage/common/storage-network-security-virtual-networks)
- [Authorize Storage requests with Microsoft Entra ID](https://learn.microsoft.com/en-us/rest/api/storageservices/authorize-with-azure-active-directory)
- [Managed identities on Azure VMs](https://learn.microsoft.com/en-us/entra/identity/managed-identities-azure-resources/how-managed-identities-work-vm)
