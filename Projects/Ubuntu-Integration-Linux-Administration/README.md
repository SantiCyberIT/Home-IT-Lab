# Lab 04 — Ubuntu Integration and Linux Administration

**Status:** Network connectivity and split DNS verified; Linux account and permission audit **in progress**  
**Work performed:** October 24, 2026  
**Type:** Independent home-lab implementation and verification  
**Related projects:** [Active Directory Corporate Environment](../Active-Directory-Environment/README.md) · [Lab 03 — DNS Troubleshooting](../Network-DNS-Troubleshooting/README.md)

## Objective

Integrate an existing Ubuntu Linux VM into the same isolated VirtualBox corporate lab as Windows Server 2025 (DC01) and Windows 11 (CLIENT01), while retaining Ubuntu's internet access and its previously created local Linux user environment.

This lab prepares a realistic, mixed-operating-system platform for troubleshooting, access-control audits, log analysis, and future defensive security investigations. **Joining Ubuntu to Active Directory was not part of this phase; DNS integration is not equivalent to AD domain membership.**

## Environment and topology

```text
                 Windows 11 virtualization host
                      Oracle VirtualBox
                              |
                    Internal network: CyberLab
                         192.168.56.0/24
                       /        |          \
                      /         |           \
   DC01                         CLIENT01       Ubuntu-Lab
   Windows Server 2025          Windows 11      Ubuntu Linux
   192.168.56.10                192.168.56.20   enp0s8: 192.168.56.30
   AD DS and DNS                AD-joined       internal DNS via DC01
   corp.santicyber.test
                                                    |
                                               enp0s3: NAT
                                               10.0.2.15/24
                                                    |
                                         External/public DNS & internet
```

| System / interface | Address | Function |
| --- | --- | --- |
| DC01 | `192.168.56.10/24` | Windows Server 2025, Active Directory and authoritative internal DNS |
| CLIENT01 | `192.168.56.20/24` | Existing Windows 11 domain-joined client (not required to be powered on for these steps) |
| Ubuntu-Lab `enp0s8` | `192.168.56.30/24` | VirtualBox Adapter 2: internal network `CyberLab`; no default gateway |
| Ubuntu-Lab `enp0s3` | `10.0.2.15/24` | VirtualBox Adapter 1: NAT; retains external connectivity and public DNS |
| Corporate DNS namespace | `corp.santicyber.test` | Queries directed to DC01 at `192.168.56.10` |

The Ubuntu VM and existing user accounts predate Lab 04. This work **reused** the VM rather than rebuilding it.

## Implementation

### 1. Connect Ubuntu to the isolated lab

In VirtualBox, kept **Adapter 1 = NAT** and set **Adapter 2 = Internal Network**, name `CyberLab`. Configured Ubuntu's second interface, `enp0s8`, with the static address `192.168.56.30/24` using Ubuntu's graphical network settings. Left the lab adapter's gateway blank to preserve routing through NAT for internet access.

Inspected interface addressing:

```bash
ip -br addr
```

Observed the expected two active IPv4 interfaces:

```text
enp0s3   UP   10.0.2.15/24
enp0s8   UP   192.168.56.30/24
```

(The loopback interface and IPv6 link-local values are omitted from this abbreviated example.)

### 2. Verify IP connectivity to Windows Server

```bash
ping -c 4 192.168.56.10
```

ICMP echo replies from DC01 confirmed Ubuntu-to-Windows reachability over the internal `CyberLab` network. The `-c 4` argument limits the test to four echo requests; without `-c`, Linux `ping` normally continues until interrupted with Ctrl+C.

This test verified **IP communication only**, not name resolution.

### 3. Inspect DNS before changing it

```bash
resolvectl status
```

Initial inspection found external/public DNS resolvers associated with NAT `enp0s3` (including `75.75.75.75` and `75.75.76.76`), while the internal `enp0s8` connection had **no corporate DNS server configured**.

The design goal was *split DNS*: query DC01 for the corporate namespace, but continue to use NAT DNS for unrelated public names.

### 4. Configure persistent, domain-specific DNS routing

Updated the saved NetworkManager profile for the internal interface:

```bash
sudo nmcli connection modify "Wired connection 1" \
  ipv4.dns "192.168.56.10" \
  ipv4.dns-search "~corp.santicyber.test" \
  ipv4.never-default yes
```

Activated the updated profile:

```bash
sudo nmcli connection up "Wired connection 1"
```

Explanation of the critical options:

| Setting | Purpose |
| --- | --- |
| `ipv4.dns 192.168.56.10` | Assign DC01 as the DNS server for the internal adapter |
| `ipv4.dns-search "~corp.santicyber.test"` | A leading `~` defines a **routing-only** DNS domain for corporate-name queries |
| `ipv4.never-default yes` | Prevent the internal lab adapter from becoming Ubuntu's default IPv4 route |
| `connection up` | Apply the saved connection configuration |

Profile name `Wired connection 1` was the observed name on this VM. Other Linux installations may use another profile name; identify it with `nmcli connection show` before reusing the command elsewhere.

### 5. Verify internal DNS configuration

Ran:

```bash
resolvectl status
```

The `enp0s8` link showed:

```text
Current Scopes: DNS
Current DNS Server: 192.168.56.10
DNS Domain: ~corp.santicyber.test
```

The NAT interface retained its external DNS servers, demonstrating the intended per-link DNS configuration.

### 6. Test corporate and public DNS queries

**Corporate lookup:**

```bash
resolvectl query dc01.corp.santicyber.test
```

**Result:** Resolved `dc01.corp.santicyber.test` to **`192.168.56.10`** over `enp0s8`, with an observed lookup time of about **4.2 ms**.

**Public lookup:**

```bash
resolvectl query example.com
```

**Result:** Returned public IPv4/IPv6 DNS records through the NAT-side configuration, with an observed lookup time of about **28.8 ms**.

Together these checks demonstrate that the tested corporate and public DNS lookups both worked after applying split DNS. A successful public name lookup does not, on its own, prove end-to-end HTTP/HTTPS connectivity.

## Verification summary

| Check | Result | Interpretation |
| --- | --- | --- |
| Ubuntu internal IPv4 address | **Passed** — `192.168.56.30/24` on `enp0s8` | Ubuntu is configured on the corporate lab subnet |
| NAT interface retained | **Passed** — `10.0.2.15/24` on `enp0s3` | Separate NAT-side addressing remains |
| Ubuntu to DC01 ping | **Passed** — replies received | Internal IP reachability established |
| DC01 DNS server configured on `enp0s8` | **Passed** | Link-level corporate DNS configuration active |
| Routing domain `~corp.santicyber.test` | **Passed** | Corporate queries targeted at the internal DNS link |
| Lookup `dc01.corp.santicyber.test` | **Passed** — `192.168.56.10` | Internal hostname resolution verified |
| Lookup `example.com` | **Passed** — public records returned | Public hostname resolution preserved |
| Linux user/group audit | **Not started in this lab session** | Next phase of Lab 04 |
| Linux ↔ AD membership/authentication | **Not tested** | Out of scope; no domain join claimed |

## Troubleshooting and lessons learned

- The first network test used continuous `ping`; on Linux, Ctrl+C stops it and `-c 4` provides a finite repeatable test.
- IP reachability and DNS resolution are **different layers of verification**. We first tested DC01 by numeric address, then resolved its fully qualified hostname.
- Public resolvers cannot be assumed to know a private Active Directory namespace. Routing `corp.santicyber.test` queries to DC01 avoids relying on external DNS for internal records.
- The `~` prefix in the NetworkManager DNS search property is important: it specifies a **routing-only domain** rather than automatically appending a suffix to every lookup.
- Dual-homed VMs should not accidentally acquire competing default gateways; NAT and internal network roles were kept distinct.
- Existing Ubuntu local employee accounts were preserved. They should not be described as AD users until directory integration is implemented and verified.

## Next phase — Linux local access-control audit (pending)

We will inspect the previously created Linux employee environment rather than recreate it. Planned steps:

1. Inventory home directories with `ls /home` (not a complete list of accounts).
2. Inspect identity, primary group, and supplementary groups using `id santicyber` and `id <username>`.
3. Enumerate actual local account entries and distinguish service accounts from interactive users.
4. Check which accounts have elevated privileges (e.g., `sudo` group membership).
5. Examine file and directory ownership and permissions with `ls -l`, `ls -ld`, and `stat`.
6. Review access control without making broad or destructive permission changes; record findings and remediations if warranted.
7. Progress toward Linux authentication logs and security-relevant event analysis.

**No results are claimed for these pending access-control steps.** Their findings will be added after hands-on verification.

## Evidence and snapshot

Lab steps were validated live in the Ubuntu terminal. A [screenshot evidence checklist](evidence/README.md) is available; **screenshots have not yet been selected and committed to this project**.

A VirtualBox snapshot at this networking/DNS checkpoint was **recommended**; snapshot completion has not been independently confirmed.

## Skills demonstrated

VirtualBox NIC configuration; Linux interface and IP checks; basic network fault isolation; NetworkManager CLI; systemd-resolved diagnostics; route-only split DNS; cross-platform DNS integration; empirical verification and incremental technical documentation.

This project represents personal learning and experimentation in an isolated lab, not production or paid IT experience.
