# Tasks 7 & 8: Network Discovery & Advanced Nmap Assessment

## Lab Environment Details
* **Auditor / Scanner VM:** Kali Linux (`192.168.56.102/24`)
* **Target VM:** Metasploitable 2 (`192.168.56.101/24`)
* **MAC Address:** `08:00:27:0e:ed:fb`
* **Subnet & Gateway:** `192.168.56.0/24`, Gateway: `192.168.56.1`
* **Virtualization:** Oracle VirtualBox Host-Only Adapter

## Task 7: Discovery Commands Run
1. `ip route` & `ifconfig`: Network configuration identification.
2. `ping -c 3 192.168.56.101`: Target reachability test (0% packet loss).
3. `sudo arping -c 3 192.168.56.101`: Layer 2 address resolution verification.
4. `nmap -sn 192.168.56.0/24`: Active host sweep across the subnet.

## Task 8: Target Port & Service Assessment Table

| IP Address | Port | Protocol | Service | Version | Risk | Observation |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 192.168.56.101 | 21 | TCP | FTP | vsftpd 2.3.4 | Critical | Cleartext protocol; unauthenticated backdoor vulnerability |
| 192.168.56.101 | 22 | TCP | SSH | OpenSSH 4.7p1 | Medium | Legacy cryptographic suite; remote administrative shell |
| 192.168.56.101 | 23 | TCP | Telnet | Linux telnetd | High | Cleartext remote access transmitting unencrypted passwords |
| 192.168.56.101 | 53 | UDP | Domain | ISC BIND | Low | Internal DNS resolver exposing version details |
| 192.168.56.101 | 80 | TCP | HTTP | Apache 2.2.8 (Ubuntu) | Medium | Web server with PHP 5.2.4; excessive banner leakage |

## Task 8: In-Depth Service Analysis:

### 1. Port 21 (FTP - vsftpd 2.3.4)
* **What is running:** File Transfer Protocol daemon (`vsftpd 2.3.4`).
* **Why required:** File sharing and remote asset distribution.
* **Is exposure necessary:** No. SFTP/SCP over SSH provides encrypted transfers over port 22.
* **Potential impact if vulnerable:** vsftpd 2.3.4 contains a malicious backdoor (CVE-2011-2523) triggered by supplying `:)` in the username, opening an unauthenticated root shell on port 6200.
* **Remediation:** Remove or disable `vsftpd` daemon: `sudo systemctl stop vsftpd`.

### 2. Port 23 (Telnet - Linux telnetd)
* **What is running:** Linux telnet daemon providing unencrypted terminal access.
* **Why required:** Legacy remote shell management.
* **Is exposure necessary:** No. Telnet has been replaced by SSH.
* **Potential impact if vulnerable:** Traffic sniffing on the local subnet captures administrator logins and passwords in cleartext.
* **Remediation:** Purge the package: `sudo apt-get purge telnetd` or disable `openbsd-inetd`.

### 3. Port 80 (HTTP - Apache 2.2.8)
* **What is running:** Apache Hypertext Transfer Protocol web daemon.
* **Why required:** Internal web application hosting.
* **Is exposure necessary:** Yes, for web services, but must enforce HTTPS on port 443.
* **Potential impact if vulnerable:** Outdated Apache 2.2.8 is susceptible to denial of service, directory traversal, and PHP 5.2 execution exploits.
* **Remediation:** Restrict banner leakage (`ServerTokens Prod`), enforce TLS/HTTPS, and update the web server.