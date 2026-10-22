# Lab 03 — Windows Server DNS Troubleshooting

**Status:** Diagnostic investigation completed; suffix-search behavior identified as the leading explanation, with packet-level root cause not conclusively proven  
**Date:** October 22, 2026  
**Type:** Independent home-lab troubleshooting case study  
**Related project:** [Active Directory Corporate Environment](../Active-Directory-Environment/README.md)

## Objective

Investigate repeatable two-second `nslookup` timeouts in a small Active Directory lab, determine which DNS functions were actually working, isolate the conditions that triggered the symptom, and document evidence without making unverified configuration changes.

This was an **unexpected issue encountered in a functioning home lab**, not a pre-scripted incident. It is not professional employment experience.

## Environment

| Component | Configuration |
| --- | --- |
| Virtualization | Oracle VirtualBox; internal network `CyberLab` |
| Domain controller | DC01 — Windows Server 2025, AD DS + DNS, `192.168.56.10` |
| Workstation | CLIENT01 — Windows 11 Pro, `192.168.56.20` |
| Active Directory domain | `corp.santicyber.test` |
| Network | `192.168.56.0/24`; isolated, no configured default gateway |
| CLIENT01 DNS server | `192.168.56.10` |

## Initial symptoms

- CLIENT01 successfully pinged DC01: **4 packets sent, 4 received, 0 lost**.
- The domain's DNS A record and the AD LDAP SRV record returned expected results, but some queries first displayed `DNS request timed out. timeout was 2 seconds.`
- Early `nslookup` output showed `Server: UnKnown`, even though the DNS server's address was correctly identified as `192.168.56.10`.
- The issue reproduced when queries were run directly from DC01, so it was not unique to the workstation-to-server path.

Sample symptom:

```console
nslookup -type=A corp.santicyber.test 127.0.0.1
Server:  localhost
Address: 127.0.0.1

DNS request timed out.
    timeout was 2 seconds.
Name:    corp.santicyber.test
Address: 192.168.56.10
```

## Investigation and evidence

### 1. Verify addressing, connectivity, and domain DNS

Checked `ipconfig /all` on both systems, confirmed static network configuration, and verified reachability with `ping 192.168.56.10`. The DNS A record for the domain and the LDAP SRV record pointing to `dc01.corp.santicyber.test` (port 389) resolved.

**Finding:** The server was reachable and authoritative internal DNS records could be obtained. A timeout message did not mean every lookup failed.

### 2. Investigate the `Server: UnKnown` message

DNS Manager initially had no IPv4 reverse lookup zone for `192.168.56.0/24`. Created the AD-integrated reverse lookup zone `56.168.192.in-addr.arpa` and added a PTR record:

```text
192.168.56.10 -> dc01.corp.santicyber.test
```

After verification, `nslookup 192.168.56.10 192.168.56.10` showed DC01's hostname and address.

**Finding:** Reverse DNS and DNS-server hostname identification were corrected. This did **not** resolve the remaining A-record timeout, so the PTR change was documented as a separate configuration improvement rather than a complete fix.

### 3. Compare query locations and protocols

Used the same A-record query from CLIENT01 and DC01, then compared local loopback and transport behavior.

| Test | Observed outcome |
| --- | --- |
| CLIENT01: normal A lookup against `192.168.56.10` | Timed out, then returned `192.168.56.10` |
| DC01: normal A lookup against `192.168.56.10` | Timed out, then returned `192.168.56.10` |
| DC01: normal A lookup against `127.0.0.1` | Three out of three repeated tests showed the two-second timeout, then returned the address |
| DC01: `nslookup -vc -type=A corp.santicyber.test 127.0.0.1` | Three out of three returned the address without a displayed timeout |

`-vc` forces TCP; the ordinary query used UDP. This suggested a transport-related difference, **but did not prove UDP port 53 was blocked**.

### 4. Inspect DNS debug logs

Temporarily enabled Windows DNS Server debug logging for incoming/outgoing UDP queries and responses, reproduced the issue, then disabled logging.

The log contained matching UDP receive/send pairs, including one query for `wpad.corp.santicyber.test` from CLIENT01 and an `NXDOMAIN` response. This showed the DNS service **could receive and answer UDP requests**.

More importantly, another request from `127.0.0.1` contained this expanded name:

```text
(4)corp(10)santicyber(4)test(4)corp(10)santicyber(4)test(0)
```

This represents:

```text
corp.santicyber.test.corp.santicyber.test
```

A matching response used the same transaction ID (`0x0002`) and returned `NXDOMAIN`—appropriate because that doubled name did not exist.

**Finding:** An unintended DNS suffix-expanded query was demonstrably occurring. The observed receive/response pair did not, by itself, establish which request caused the two-second wait.

### 5. Control DNS suffix searching

The following comparisons were made without changing DNS Server settings:

```cmd
:: Normal lookup: repeatedly displayed a timeout
nslookup -type=A corp.santicyber.test 127.0.0.1

:: Absolute DNS name (trailing dot): succeeded without displayed timeout
nslookup -type=A corp.santicyber.test. 127.0.0.1

:: Disable suffix search: succeeded without displayed timeout
nslookup -nosearch -type=A corp.santicyber.test 127.0.0.1
```

The `-nosearch` result was also verified on CLIENT01 against `192.168.56.10`, where the query returned the expected A record without a displayed timeout.

## Assessment

**Confirmed:** Network reachability; correct internal A/SRV records; functional reverse PTR mapping after configuration; reproducible timeout with ordinary `nslookup`; successful controlled lookups using TCP, a trailing dot, or `-nosearch`; suffix-expanded queries visible in DNS Server logs.

**Leading explanation:** Windows `nslookup` search-suffix processing generated unintended expanded hostname queries and was associated with the additional wait seen during normal queries. The isolated network and additional external-forwarded DNS queries are possible relevant context.

**Limit of certainty:** We did not capture and correlate every timed-out transaction end-to-end. Therefore this is **strong evidence of a suffix-search-related symptom**, not proof that the doubled `NXDOMAIN` transaction itself consumed the two seconds or that the DNS service was globally unhealthy.

No firewall disablement, global DNS suffix changes, or broad DNS service reconfiguration was performed to mask the symptom. The controlled lookups succeeded; default `nslookup` behavior may merit follow-up if it affects real client applications.

## Operational lessons

1. Separate a *successful final DNS answer* from a *timely DNS response*.
2. Validate reachability, reverse mapping, record types, and client/server vantage points before changing settings.
3. Compare UDP and TCP without assuming a protocol is fully blocked.
4. Correlate DNS requests and responses by transaction ID, source/destination, name, and response code.
5. A trailing dot marks an absolute DNS name; `-nosearch` helps isolate DNS search-suffix effects.
6. Document both confirmed results and unresolved causes; disable noisy debug logging after a focused test.

## Evidence and follow-up

- [Existing AD environment screenshots](../Active-Directory-Environment/evidence) show the deployed systems and earlier connectivity checks.
- [Lab 03 evidence folder](evidence) is reserved for selected screenshots from this investigation. Screenshots have not yet been added to this folder.
- If more proof is needed, capture a tightly scoped request/response trace and compare the exact timed-out query IDs; investigate DNS suffix list and server recursion/forwarder behavior in the isolated network.

**Skills demonstrated:** systematic network fault isolation, Active Directory DNS management, forward/reverse DNS, UDP/TCP testing, log filtering, packet field interpretation, evidence-based reporting.
