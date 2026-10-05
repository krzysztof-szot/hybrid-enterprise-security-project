# Day 12 — Network access test record

**Executed by:** project owner, 2026-10-05.
**Status:** recorded functional scope completed. No Azure tests were rerun during documentation.
[Implementation and scripts](../docs/day-12.md) · [8 screenshots](../evidence/day-12/README.md) · [Session output](../evidence/day-12/console-excerpts.md).

## Preconditions

- vnet-bfl-day12 contains snet-client 10.120.1.0/24 and snet-server 10.120.2.0/24 (01).
- nsg-bfl-day12-server is associated with snet-server (02).
- Both VMs were Running before testing, owner-confirmed.
- Server listener active at 10.120.2.4:8080, supported by supplied command output.
- Client 10.120.1.4 runs the same Python HTTP script for all three application tests.
- Each HTTP execution opens a new connection without an HTTP proxy.

## Results

| ID | Procedure / precondition | Expected result | Actual result / status | Evidence |
|---|---|---|---|---|
| NET-01 | With default server NSG rules, request http://10.120.2.4:8080/ from client | HTTP 200 and correct test body | **PASS:** HTTP 200, expected body, PASS marker | [03](../evidence/day-12/03-client-server-allowed.png) |
| NET-02 | Add inbound TCP deny, client /32 to server /32, destination 8080, priority 100; repeat request | New connection blocked | **PASS:** timeout and URLError; no HTTP 200 | [04](../evidence/day-12/04-nsg-deny-rule.png), [05](../evidence/day-12/05-client-server-blocked.png) |
| NET-03 | Run IP flow verify on server NIC: inbound TCP, local 10.120.2.4:8080, remote 10.120.1.4:60000 | Denied by custom rule | **PASS:** Access denied, Deny-Client-To-Server-8080, nsg-bfl-day12-server | [06](../evidence/day-12/06-ip-flow-denied.png) |
| NET-04 | Remove custom deny rule; repeat unchanged HTTP script | Application access restored | **PASS:** HTTP 200, expected body, PASS marker | [07](../evidence/day-12/07-client-server-restored.png) |
| NET-05 | Repeat IP flow verify with same tuple after removal | Allowed by default intra-VNet rule | **PASS:** Access allowed, AllowVnetInBound | [08](../evidence/day-12/08-ip-flow-allowed.png) |

The HTTP response demonstrates actual communication. IP flow verify is a security-rule evaluation, not a second application request. Source port 60000 was selected for that evaluation; the HTTP client's ephemeral source port was not recorded.

## Supporting checks and final state

| Item | Result and evidence type |
|---|---|
| Two intended subnets | Verified by screenshot 01 |
| Server NSG association | Verified by screenshot 02 |
| Server HTTP listener | Supplied output: active, LISTEN at 10.120.2.4:8080 |
| Test rule removed | Functional recovery and default-rule result in 07–08 support the recorded removal step |
| Both VMs Stopped (deallocated) | Owner-confirmed after the tests; no final status screenshot |
| Final client NIC NSG and delete-with-VM flags | Correction instructed; final configuration not independently verified |

## Boundaries

An Enable succeeded banner means the extension invocation completed; the actual HTTP output or error determines the application result. Timeout is consistent with the deny and is corroborated by the matching-rule diagnostic and successful recovery.

No PASS is claimed for Internet ingress protection, other ports/protocols, exact propagation timing, all effective security rules, post-restart service startup, quota remediation details or actual costs. Test resources were not reported deleted. Deallocation is not evidence of deletion or zero ongoing storage/IP charges.
