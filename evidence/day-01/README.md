# Day 01 evidence inventory

**Screenshots have not yet been uploaded to this repository.** No image placeholders or links to nonexistent files are used.

## Available

[console-excerpts.md](console-excerpts.md) contains selected actual output supplied by the owner: network, domain/forest, services, shares, DNS, accounts, membership, DC diagnostics, time repair and activation. It is a curated transcript, not an automated rerun or exported event log.

## Screenshot upload checklist

The filenames below are proposed names, not existing files.

| Proposed filename | What to capture | What it establishes | Status |
|---|---|---|---|
| 01-vmnet8-network.png | Virtual Network Editor with VMnet8, subnet and mask | NAT network configuration | Reviewed in chat; upload pending |
| 02-vmnet8-nat.png | NAT settings and gateway | Gateway 192.168.58.2 | Reviewed in chat; upload pending |
| 03-vmnet8-dhcp.png | DHCP start/end range | Static DC address is outside DHCP pool | Reviewed in chat; upload pending |
| 04-ad-ou-structure.png | AD Users and Computers, expanded BFL tree including Groups | Actual OU hierarchy | Capture/upload not confirmed |
| 05-dns-zones.png | DNS Manager, expanded Forward Lookup Zones | Presence of domain DNS zones | Capture/upload not confirmed |
| 06-ad-users.png | Departmental users and separate Admins OU, with extra captures if needed | Account placement | Optional; capture/upload not confirmed |
| 07-ad-groups.png | Groups OU and relevant membership views | Group configuration and members | Optional; capture/upload not confirmed |

The Domain Controller Options screenshot was reviewed during setup but is not uploaded. It establishes intended settings only; post-promotion command output establishes actual domain state.

Before upload, inspect each complete image for credentials, private account details and unnecessary identifiers. Preserve useful lab names and private lab addressing. Never upload DSRM passwords, password-manager screens, tokens, ISO files, VM disks or snapshots. Add Markdown image links only after filenames are verified in GitHub.

## Evidence limitations

No Windows event-log exports, client logon evidence, negative authorization results, or cross-DC replication evidence were collected. Do not label planned tests as passed.
