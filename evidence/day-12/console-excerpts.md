# Day 12 — Selected session output

**Session:** 2026-10-05. These excerpts transcribe the owner's output and screenshots; no new commands were executed for this document.

[Implementation and scripts](../../docs/day-12.md) · [Tests](../../tests/day-12.md) · [Screenshots](README.md)

## Owner confirmations

- New resource group rg-bfl-network-lab created.
- More than EUR 156 credit remaining, expiring 2026-10-13; not a billing export.
- Server Running with private IP 10.120.2.4.
- Client Running with private IP 10.120.1.4.
- Both VMs Stopped (deallocated) after testing.

## Server listener

Selected supplied stdout:

```text
/usr/bin/python3
active
State  Recv-Q Send-Q Local Address:Port Peer Address:Port
LISTEN 0      5      10.120.2.4:8080    0.0.0.0:*
```

The process field identified python3. The stderr message named bfl-day12-http.service as the newly started unit; its invocation identifier is omitted. This was a service-start message, not an error.

## Initial allowed request — screenshot 03

```text
HTTP status: 200
Response: Baltic Finance - Day 12 network test
PASS: client-to-server HTTP connection allowed.
```

The reported stderr was empty.

## Denied request — screenshot 05

Selected exception lines:

```text
TimeoutError: timed out
urllib.error.URLError: <urlopen error timed out>
```

The Python traceback is not reproduced in full. The same request targeted http://10.120.2.4:8080/ with a ten-second timeout.

## Denied rule evaluation — screenshot 06

```text
Protocol: TCP
Direction: Inbound
Local IP: 10.120.2.4
Local port: 8080
Remote IP: 10.120.1.4
Remote port: 60000
Result: Access denied
Security Rule: Deny-Client-To-Server-8080
Network Security Group: nsg-bfl-day12-server
```

## Restored request — screenshot 07

```text
HTTP status: 200
Response: Baltic Finance - Day 12 network test
PASS: client-to-server HTTP connection allowed.
```

## Allowed rule evaluation — screenshot 08

The same diagnostic tuple returned:

```text
Result: Access allowed
Security Rule: AllowVnetInBound
```

## Interpretation

Run Command's Enable succeeded banner is not an HTTP test result. The application output, explicit exception, matching-rule evaluation and subsequent recovery together support the recorded sequence. No packet capture or exact propagation measurement was supplied.
