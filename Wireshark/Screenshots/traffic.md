# Tasks 9, 10 & 11: Network Traffic Analysis & Investigation

## Task 9: Protocol Breakdown
* **DNS:** Captured queries using `dig @192.168.56.101 example.com` returning transaction ID `10056` and status `SERVFAIL`.
* **TCP:** Captured 3-way handshakes, half-open SYN probes, and teardown RST sequences.
* **UDP:** Observed DNS query/response packets on port 53.
* **ICMP:** Verified baseline echo request/reply packets via `ping -c 3 192.168.56.101`.
* **ARP:** Validated Layer 2 address mappings using `arping` resolving `08:00:27:0e:ed:fb`.
* **HTTP:** Analyzed HTTP `HEAD /` request with `200 OK` response.

## Task 10: Nmap vs. Wireshark Correlation Table
| Nmap Result | Wireshark Packet Evidence | Protocol Interpretation |
| :--- | :--- | :--- |
| **Port 21 Open** | Packet 56: Target sends `[SYN, ACK]`; Packet 58: Scanner sends `[RST]` | The port is actively listening; scanner tears down half-open connection |
| **Port 80 Open** | Packet 57: Target sends `[SYN, ACK]`; Packet 59: Scanner sends `[RST]` | Port is listening; scanner uses half-open probe to confirm open state |
| **Port 9999 Closed**| Packet 55: Scanner sends `[SYN]`; Packet 60: Target sends `[RST, ACK]` | Port has no active daemon; target kernel resets connection |

## Task 11: Incident & Traffic Investigation Log

| Time | Source IP | Destination IP | Port | Protocol | Observation | Security Significance |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| `146.468s` | 192.168.56.101 | 192.168.56.102 | 21 | FTP | Server sends banner: `220 (vsFTPd 2.3.4)` | Critical version disclosure exposing known RCE backdoor |
| `191.188s` | 192.168.56.102 | 192.168.56.101 | 80, 21, 9999 | TCP | Rapid succession of `SYN` packets from source port `44562` | Network reconnaissance probe signature (Nmap SYN scan) |
| `191.189s` | 192.168.56.101 | 192.168.56.102 | 9999 | TCP | Immediate `[RST, ACK]` response flag | Host is active but rejecting traffic on closed port |
| `124.755s` | 192.168.56.102 | 192.168.56.101 | 80 | HTTP | Client requests `HEAD / HTTP/1.1` | Web service enumeration probe without retrieving full body |
| `13:55:11` | 192.168.56.102 | 192.168.56.101 | 53 | UDP | Query for `example.com` returning `SERVFAIL` | Probing target DNS daemon for open resolver / misconfiguration |

### Statistical Metrics
* **Top Communicating Hosts:** `192.168.56.102` (Auditor) and `192.168.56.101` (Target).
* **TCP Stream Analysis:**
  * Stream 0 (Port 80): 11 packets (Full HTTP session).
  * Stream 1 (Port 21): 9 packets (Full FTP interactive login attempt).
  * Streams 2–4: Short 2–3 packet bursts (Nmap SYN scan probes).