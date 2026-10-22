# Lab 03 — Evidence Images

Place selected screenshots for the [DNS troubleshooting investigation](../README.md) in this folder.

## Recommended screenshots (not yet uploaded)

| Suggested filename | Show this evidence |
| --- | --- |
| `01-original-timeout.png` | DC01 or CLIENT01 normal `nslookup -type=A corp.santicyber.test` showing two-second timeout and eventual A-record answer |
| `02-reverse-ptr-record.png` | DNS Manager reverse zone showing DC01 PTR pointing to `dc01.corp.santicyber.test` |
| `03-udp-tcp-comparison.png` | Repeated UDP timeouts compared with successful `-vc` queries |
| `04-dns-debug-suffix-expanded.png` | Matching UDP receive/send entries with doubled hostname and `NXDOMAIN` |
| `05-nosearch-client-verification.png` | CLIENT01 normal lookup compared to successful `-nosearch` lookup |

Choose readable crops. Remove any passwords, recovery keys, secrets, personal information, or unrelated desktop content before publishing to this **public** repository.

The case study records observations already made. This folder is for supporting images; **no screenshots are claimed as uploaded until they are actually present**.
