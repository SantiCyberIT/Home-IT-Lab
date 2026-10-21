# Active Directory Corporate Environment

**Project status:** Core domain and workstation authentication verified (October 2026)  
**Environment:** Isolated VirtualBox home lab  
**Focus:** Windows Server administration, Active Directory, DNS, Windows 11 deployment, domain authentication, troubleshooting

## Overview

Built a small, fictional corporate Windows environment to practice skills relevant to IT support, desktop support, network troubleshooting, and later security investigations. The environment contains a Windows Server 2025 domain controller and a Windows 11 Pro employee workstation. The workstation was joined to the domain, and an employee's domain sign-in was verified.

This is **independent home-lab work**, not professional IT employment. Employee identities and the domain are fictional.

## Architecture

```text
Windows 11 host / Oracle VirtualBox
              |
     Internal network: CyberLab
          192.168.56.0/24
              |
      +-------+--------+
      |                |
  DC01                   CLIENT01
  Windows Server 2025    Windows 11 Pro
  192.168.56.10          192.168.56.20
  AD DS + DNS            DNS: 192.168.56.10
  corp.santicyber.test   Domain-joined workstation
```

- Domain: `corp.santicyber.test` (NetBIOS: `CORP`)
- DC01: 6 GB RAM, 2 vCPUs, 80 GB virtual disk; internal adapter attached to `CyberLab`
- CLIENT01: 6 GB RAM, 2 vCPUs, 80 GB dynamically allocated virtual disk; EFI, virtual TPM 2.0, Guest Additions
- Static IPv4 configuration; subnet mask `255.255.255.0`; no default gateway on the isolated network
- DC01 resolves DNS locally through its loopback interface; CLIENT01 queries DC01 at `192.168.56.10`

## Implementation

1. Installed Windows Server 2025 and configured the DC01 hostname and static IPv4 networking.
2. Installed Active Directory Domain Services and DNS; promoted DC01 for the `corp.santicyber.test` forest.
3. Validated domain-controller and DNS functionality using `nltest /dsgetdc:corp.santicyber.test` and `dcdiag /test:dns`.
4. Created CLIENT01 as a Windows 11 Pro VM with appropriate virtual hardware and an internal-network connection.
5. Configured CLIENT01 with static IPv4 `192.168.56.20/24` and DNS `192.168.56.10`.
6. Verified IP connectivity and Active Directory DNS discovery before attempting domain join.
7. Joined CLIENT01 to `corp.santicyber.test` using the Windows computer/domain membership dialog and authorized domain administrator credentials.
8. Created a fictional employee identity, **Sally MorganX** (`CORP\smorgan`), and signed in to CLIENT01 using the domain account.

## Verification evidence

| Verification | Result |
| --- | --- |
| CLIENT01 to DC01 `ping 192.168.56.10` | 4 sent, 4 received, 0 lost |
| `nslookup corp.santicyber.test` | Resolved to `192.168.56.10` |
| `nslookup -type=SRV _ldap._tcp.dc._msdcs.corp.santicyber.test` | Returned `dc01.corp.santicyber.test` at `192.168.56.10`, LDAP port 389 |
| Domain join | Windows displayed the welcome-to-domain confirmation |
| Windows domain membership | Remained present after reboot/reset |
| DC01 Active Directory Users and Computers | `CLIENT01` appeared in the **Computers** container |
| CLIENT01 PowerShell `Test-ComputerSecureChannel` | `True` |
| Employee session `whoami` | `corp\smorgan` |
| Employee session `echo %LOGONSERVER%` | `\\DC01` |

**Screenshot status:** Verification screenshots were captured during the lab conversation; repository evidence images are not yet uploaded or linked here. They will be added once selected and sanitized.

## Troubleshooting and lessons learned

**Guest display resolution:** The Windows 11 guest initially displayed black borders or an improperly scaled desktop. Guest Additions was installed, and switching VirtualBox display modes eventually produced a correctly fitting full-screen display. No rebuild was needed.

**DNS lookup timeout:** An initial two-second timeout and `Server: UnKnown` appeared in `nslookup`, while both the domain A-record lookup and LDAP SRV lookup ultimately returned correct answers. The initial server-identification/reverse-lookup behavior may merit later investigation; a specific root cause has **not** been confirmed.

**Frozen restart after domain join:** CLIENT01 displayed a successful domain-join welcome message but froze during the subsequent restart. After resetting the unresponsive VM, Windows booted successfully and retained domain membership. The secure-channel check also passed. The underlying cause of the freeze was not established.

**Password policy rejection:** An initial password choice for the fictional employee account was rejected by Active Directory policy. A compliant password was used without weakening the domain policy.

**Command syntax correction:** Two verification commands were initially entered as one command and returned a syntax error. Executing `whoami` and `echo %LOGONSERVER%` separately returned the expected values.

## Skills demonstrated

- Virtual machine deployment and resource allocation
- Static IPv4 addressing, network connectivity checks, and DNS troubleshooting
- Windows Server AD DS and DNS configuration
- Active Directory computer and employee-account administration
- Domain joining, trust verification, and employee authentication
- Interpreting command-line evidence and documenting troubleshooting outcomes

## Next work

- Upload sanitized screenshots supporting the verification table
- Add focused build notes for DC01 and CLIENT01 as needed
- Establish a sound DC01 backup and recovery procedure before disruptive experiments
- Reuse the environment for group policy, permissions, help-desk incidents, network investigations, and defensive monitoring

The project is deliberately incremental: infrastructure is the foundation for operational and investigative scenarios, not the end goal.
