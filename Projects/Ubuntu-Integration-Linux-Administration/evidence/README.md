# Lab 04 — Evidence checklist

Evidence screenshots for [Ubuntu Integration and Linux Administration](../README.md) can be added to this folder after review and selection.

**Status:** Screenshot selection/upload pending. This checklist does not imply that image files are present.

## Suggested captures

1. **01-virtualbox-ubuntu-network-adapters.png** — Adapter 1 NAT and Adapter 2 Internal Network `CyberLab`, if an existing screenshot captures both.
2. **02-ubuntu-ip-addresses.png** — `ip -br addr`: `enp0s3` at `10.0.2.15/24`, `enp0s8` at `192.168.56.30/24`.
3. **03-ubuntu-dc01-ping.png** — finite successful `ping -c 4 192.168.56.10` or retained earlier successful ping evidence.
4. **04-ubuntu-dns-before.png** — original `resolvectl status`, internal link without corporate DNS.
5. **05-ubuntu-dns-after.png** — updated `resolvectl status`, `DNS Servers: 192.168.56.10` and `DNS Domain: ~corp.santicyber.test` under `enp0s8`.
6. **06-ubuntu-internal-dns-lookup.png** — `resolvectl query dc01.corp.santicyber.test` yielding `192.168.56.10`.
7. **07-ubuntu-public-dns-lookup.png** — `resolvectl query example.com` yielding public addresses.
8. **08-ubuntu-linux-account-audit.png** — *pending work*, to be collected only when the audit is actually performed.

Only publish screenshots that substantiate a concrete step or result; omit duplicates or unneeded images. Redact personal identifiers, tokens, and passwords before posting.

The VM snapshot was recommended as a rollback checkpoint but is not verified or represented by a screenshot here.
