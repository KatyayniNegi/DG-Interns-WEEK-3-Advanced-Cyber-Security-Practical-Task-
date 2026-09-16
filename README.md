# Network Security Assessment & Traffic Investigation

**Role:** Cybersecurity Intern  
**Repository:** `cybersecurity-week-3-network-security`  
**Lab Environment:** Kali Linux (`192.168.56.102`) & Metasploitable 2 (`192.168.56.101`)  

## Project Overview
This project presents an end-to-end network security audit, packet-level traffic investigation, and remediation plan for an authorized virtualized laboratory environment.

## Lab Architecture & Topology
* **Auditor VM:** Kali Linux (`192.168.56.102/24`)
* **Target VM:** Metasploitable 2 (`192.168.56.101/24`)
* **Network Segment:** `192.168.56.0/24` (VirtualBox Host-Only Adapter)

## Tasks Completed
* **Task 7:** Network discovery using `ip route`, `arping`, `ping`, and Nmap host sweeping.
* **Task 8:** Advanced port and service identification (`-sV`, `-O`, `-sU`, NSE scripts).
* **Task 9:** Protocol packet capture across DNS, TCP, UDP, ICMP, ARP, and HTTP.
* **Task 10:** Wire-level Nmap scan correlation (analyzing `SYN`, `SYN-ACK`, and `RST` packets).
* **Task 11:** Traffic analysis isolating top talkers, connection streams, and service banner leaks.
* **Task 12:** Risk classification of findings across Critical, High, Medium, and Low tiers.
* **Task 13:** Security hardening with verifiable Before/After Nmap scans and socket audits.
* **Task 14:** Synthesis of findings into formal documentation and presentations.

## Key Findings Summary
1. **Critical:** vsftpd 2.3.4 unauthenticated backdoor vulnerability on port 21 (CVE-2011-2523).
2. **High:** Unencrypted remote terminal access via Telnet on port 23.
3. **Medium:** Outdated Apache 2.2.8 and PHP 5.2.4 web stack leaking software versions.
4. **Medium:** Permissive ingress firewall policy with zero packet filtering.

## Hardening Verification
| Metric | Pre-Hardening State | Post-Hardening State |
| :--- | :--- | :--- |
| **Port 21 (FTP)** | `OPEN` (vsftpd 2.3.4) | `CLOSED` |
| **Port 23 (Telnet)** | `OPEN` (Linux telnetd) | `CLOSED` |
| **Port 80 (HTTP)** | `OPEN` (Apache 2.2.8) | `CLOSED` |
| **Attack Surface** | 5+ listening network daemons | Reduced to isolated SSH access |

## Professional Recommendations
1. Enforce default-deny inbound firewall policies across all lab subnets.
2. Decommission legacy cleartext administrative services (Telnet/FTP) in favor of SSH/SFTP.
3. Suppress server version disclosure banners across all listening daemons.
4. Apply routine patch management and vulnerability remediation schedules.
5. Deploy Network Intrusion Detection Systems (NIDS) to flag unauthorized half-open port sweeps.