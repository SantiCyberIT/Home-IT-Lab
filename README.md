# Home IT Lab — SantiCyberIT

Hands-on Windows, Linux, networking, and cybersecurity projects built in an isolated virtual lab. My goal is to develop practical troubleshooting, system administration, and blue-team investigative skills while pursuing a cybersecurity degree and industry certifications.

This repository documents **independent learning and home-lab work**, not paid professional IT employment.

## Featured project

### [Active Directory Corporate Environment](Projects/Active-Directory-Environment/README.md)
**Core infrastructure and client authentication verified — October 2026**

- Deployed Windows Server 2025 with Active Directory Domain Services and DNS
- Built and joined a Windows 11 Pro client to `corp.santicyber.test`
- Verified IP reachability, DNS discovery, domain membership, and secure channel
- Created a fictional employee account and verified domain sign-in
- Documented troubleshooting encountered during deployment

[Read project overview, evidence table, and troubleshooting notes →](Projects/Active-Directory-Environment/README.md)

## Troubleshooting case studies

### [Lab 03 — Windows Server DNS Troubleshooting](Projects/Network-DNS-Troubleshooting/README.md)
**Investigated October 22, 2026 — controlled DNS resolution verified; root cause not fully proven**

- Reproduced a two-second `nslookup` timeout from both DC01 and CLIENT01
- Configured and verified a reverse DNS zone and PTR record for DC01
- Compared normal UDP and forced TCP DNS queries
- Reviewed DNS debug logs, matched request/response IDs, and discovered a duplicated DNS search suffix
- Verified lookups without the timeout using a trailing dot or `-nosearch`
- Documented the remaining uncertainty rather than overstating the diagnosis

[Read the investigation, commands, results, and evidence plan →](Projects/Network-DNS-Troubleshooting/README.md)

## Environment

| Component | Implementation |
| --- | --- |
| Host | Windows 11; AMD Ryzen 7 5700G; 32 GB RAM |
| Virtualization | Oracle VirtualBox |
| Domain controller | DC01 — Windows Server 2025, AD DS and DNS, `192.168.56.10` |
| Domain | `corp.santicyber.test` (NetBIOS `CORP`) |
| Workstation | CLIENT01 — Windows 11 Pro, `192.168.56.20` |
| Lab network | VirtualBox internal network `CyberLab`, isolated by default |
| Linux | Existing Ubuntu VM; integration review planned |

## Foundational learning archive

My earlier Linux and virtualization exercises remain available in [Documentation](Documentation). They cover VM foundations, Linux navigation, file management, permissions, and user/group administration.

Supporting screenshots from those earlier lessons are in [Screenshots](Screenshots); existing diagrams are in [Network-Diagrams](Network-Diagrams).

## Roadmap

The existing Windows domain is a platform for realistic IT support scenarios, networking troubleshooting, Windows event-log analysis, help-desk workflows, SIEM/blue-team alert triage, and eventually incident-response investigations.

Only completed and verified project outcomes are presented as achievements. New investigations and projects will be linked here as evidence becomes available.

## Santi CyberSec videos

Recorded demonstrations are being edited separately. Published videos will be linked from the corresponding project pages when available.
