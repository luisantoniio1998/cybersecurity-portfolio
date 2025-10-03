# Analyze Network Layer Communication

## Overview

**Date**: September 14, 2024
**Analyst**: Luís Barros
**Tools Used**: tcpdump, Wireshark
**Objective**: Analyze network traffic at various OSI layers to understand protocols, identify security issues, and investigate potential threats

---

## Introduction to Network Layer Analysis

Network layer communication analysis involves examining data packets as they traverse through different layers of the OSI (Open Systems Interconnection) model. By capturing and analyzing network traffic, security analysts can:

- Identify normal vs. abnormal network behavior
- Detect security threats and suspicious activity
- Troubleshoot network connectivity issues
- Understand protocol interactions
- Investigate security incidents

This document demonstrates analysis of network traffic using packet capture tools like tcpdump and Wireshark.

---

## Part 1: TCP/IP Model and Network Layers

### OSI Model vs. TCP/IP Model

**OSI Model (7 Layers)**:
1. **Application** - HTTP, DNS, SMTP, FTP
2. **Presentation** - Encryption, compression
3. **Session** - Session management
4. **Transport** - TCP, UDP
5. **Network** - IP, ICMP, routing
6. **Data Link** - Ethernet, MAC addresses, switches
7. **Physical** - Cables, signals, bits

**TCP/IP Model (4 Layers)**:
1. **Application** - HTTP, DNS, SMTP (OSI 5-7)
2. **Transport** - TCP, UDP (OSI 4)
3. **Internet** - IP, ICMP (OSI 3)
4. **Network Access** - Ethernet, WiFi (OSI 1-2)

For security analysis, we focus on the TCP/IP model as it represents how internet protocols actually work.

---

## Part 2: Capture Network Traffic with tcpdump

### Scenario

A security analyst needs to capture network traffic on a Linux server to investigate potential security issues and analyze communication patterns.

### Using tcpdump to Capture Traffic

#### Basic Packet Capture

**Command**:
```bash
sudo tcpdump -i eth0 -c 100
```

**Explanation**:
- `sudo`: Root privileges required to capture packets
- `-i eth0`: Capture on network interface eth0
- `-c 100`: Capture only 100 packets

**Sample Output**:
```
14:23:15.123456 IP 192.168.1.10.54321 > 8.8.8.8.53: UDP, length 29
14:23:15.145678 IP 8.8.8.8.53 > 192.168.1.10.54321: UDP, length 45
14:23:15.234567 IP 192.168.1.10.45678 > 172.217.14.206.443: Flags [S], seq 1234567890
```

**What We See**:
- Timestamps showing when packets were captured
- Source and destination IP addresses
- Source and destination port numbers
- Protocol information (UDP, TCP)
- TCP flags (S = SYN for connection establishment)

#### Capture HTTP Traffic

**Command**:
```bash
sudo tcpdump -i eth0 -n port 80 -A
```

**Explanation**:
- `-n`: Don't resolve hostnames (faster, shows IPs)
- `port 80`: Only capture HTTP traffic
- `-A`: Display packet contents in ASCII

**Sample Output**:
```
14:25:30.123456 IP 192.168.1.10.54321 > 93.184.216.34.80: Flags [P.], seq 1:100, ack 1, win 502, length 99
E.....@.@.............P...GET /index.html HTTP/1.1
Host: www.example.com
User-Agent: Mozilla/5.0
```

**Analysis**:
- HTTP GET request to www.example.com
- Requesting /index.html page
- **Security Concern**: HTTP is unencrypted - credentials or sensitive data would be visible in plaintext!

#### Capture and Save to File

**Command**:
```bash
sudo tcpdump -i eth0 -w capture.pcap -c 1000
```

**Explanation**:
- `-w capture.pcap`: Write packets to file (can analyze later in Wireshark)
- `-c 1000`: Capture 1000 packets

**Reading the Capture File**:
```bash
tcpdump -r capture.pcap
```

---

## Part 3: Analyze DNS Traffic

### Scenario: Investigating DNS Queries

DNS (Domain Name System) traffic can reveal:
- What websites users are visiting
- Potential DNS exfiltration attempts
- Malware command and control (C2) communication
- DNS tunneling attacks

### Capturing DNS Traffic

**Command**:
```bash
sudo tcpdump -i eth0 port 53 -n -c 50
```

**Sample Output**:
```
15:10:12.123456 IP 192.168.1.10.54321 > 8.8.8.8.53: 12345+ A? www.google.com. (32)
15:10:12.145678 IP 8.8.8.8.53 > 192.168.1.10.54321: 12345 1/0/0 A 142.250.185.46 (48)
15:10:15.234567 IP 192.168.1.10.54322 > 8.8.8.8.53: 12346+ A? malicious-domain.com. (42)
15:10:15.256789 IP 8.8.8.8.53 > 192.168.1.10.54322: 12346 NXDomain 0/1/0 (98)
```

**Analysis**:

**Normal DNS Query (Line 1-2)**:
- Query: 192.168.1.10 asks DNS server (8.8.8.8) for IP address of www.google.com
- Response: DNS server returns IP 142.250.185.46
- **A?** means "A record query" (asking for IPv4 address)
- **Transaction ID**: 12345 (matches query and response)

**Suspicious DNS Query (Line 3-4)**:
- Query for "malicious-domain.com"
- Response: **NXDomain** (domain doesn't exist)
- **Red Flag**: Could indicate malware trying to contact C2 server that's been taken down

### Detecting DNS Tunneling

**Suspicious Pattern**:
```
15:15:20.123456 IP 192.168.1.42 > 8.8.8.8.53: A? ZGF0YS1leGZpbHRyYXRpb24.malicious.com.
15:15:21.123456 IP 192.168.1.42 > 8.8.8.8.53: A? Y29uZmlkZW50aWFsLWRhdGE.malicious.com.
15:15:22.123456 IP 192.168.1.42 > 8.8.8.8.53: A? c2VjcmV0LWluZm9ybWF0aW9u.malicious.com.
```

**Red Flags**:
- Very long subdomain names
- Random-looking characters (Base64 encoded)
- High frequency of queries to same domain
- Unusual query patterns

**Decoded** (Base64):
- `ZGF0YS1leGZpbHRyYXRpb24` = "data-exfiltration"
- `Y29uZmlkZW50aWFsLWRhdGE` = "confidential-data"
- `c2VjcmV0LWluZm9ybWF0aW9u` = "secret-information"

**Assessment**: This is DNS tunneling - data exfiltration through DNS queries!

---

## Part 4: Analyze TCP Three-Way Handshake

### TCP Connection Establishment

TCP uses a three-way handshake to establish reliable connections:

**Handshake Process**:
1. **SYN**: Client sends SYN (synchronize) packet to server
2. **SYN-ACK**: Server responds with SYN-ACK (synchronize-acknowledge)
3. **ACK**: Client sends ACK (acknowledge) to complete connection

### Capturing TCP Handshake

**Command**:
```bash
sudo tcpdump -i eth0 'tcp[tcpflags] & (tcp-syn) != 0' -n -c 10
```

**Sample Output**:
```
16:20:10.123456 IP 192.168.1.10.54321 > 93.184.216.34.80: Flags [S], seq 1234567890, win 65535
16:20:10.145678 IP 93.184.216.34.80 > 192.168.1.10.54321: Flags [S.], seq 9876543210, ack 1234567891, win 65535
16:20:10.146789 IP 192.168.1.10.54321 > 93.184.216.34.80: Flags [.], ack 9876543211, win 65535
```

**Analysis**:

**Packet 1 - SYN**:
- Client (192.168.1.10:54321) → Server (93.184.216.34:80)
- **Flags [S]**: SYN flag set
- **seq 1234567890**: Initial sequence number
- **Purpose**: Client initiating connection

**Packet 2 - SYN-ACK**:
- Server (93.184.216.34:80) → Client (192.168.1.10:54321)
- **Flags [S.]**: Both SYN and ACK flags set
- **seq 9876543210**: Server's sequence number
- **ack 1234567891**: Acknowledging client's seq + 1
- **Purpose**: Server accepting connection

**Packet 3 - ACK**:
- Client → Server
- **Flags [.]**: ACK flag set
- **ack 9876543211**: Acknowledging server's seq + 1
- **Purpose**: Connection established, can now exchange data

### Abnormal TCP Behavior - SYN Flood Attack

**Suspicious Pattern**:
```
16:25:00.123456 IP 10.0.0.1.12345 > 192.168.1.100.80: Flags [S], seq 111111111
16:25:00.234567 IP 10.0.0.2.12346 > 192.168.1.100.80: Flags [S], seq 222222222
16:25:00.345678 IP 10.0.0.3.12347 > 192.168.1.100.80: Flags [S], seq 333333333
16:25:00.456789 IP 10.0.0.4.12348 > 192.168.1.100.80: Flags [S], seq 444444444
... (hundreds more)
```

**Red Flags**:
- Massive number of SYN packets in short time
- Different source IPs (could be spoofed)
- No corresponding ACK packets (connections not completed)
- **Assessment**: SYN flood DDoS attack attempting to exhaust server resources!

---

## Part 5: Analyze ICMP Traffic

### ICMP (Internet Control Message Protocol)

ICMP is used for network diagnostics and error messages:
- **ping**: Test connectivity (ICMP Echo Request/Reply)
- **traceroute**: Trace packet path (ICMP Time Exceeded)
- **Error messages**: Destination unreachable, TTL exceeded

### Capturing ICMP Traffic

**Command**:
```bash
sudo tcpdump -i eth0 icmp -n
```

**Normal Ping Traffic**:
```
17:00:10.123456 IP 192.168.1.10 > 8.8.8.8: ICMP echo request, id 1234, seq 1, length 64
17:00:10.145678 IP 8.8.8.8 > 192.168.1.10: ICMP echo reply, id 1234, seq 1, length 64
17:00:11.123456 IP 192.168.1.10 > 8.8.8.8: ICMP echo request, id 1234, seq 2, length 64
17:00:11.145678 IP 8.8.8.8 > 192.168.1.10: ICMP echo reply, id 1234, seq 2, length 64
```

**Analysis**:
- Request/Reply pairs indicate successful communication
- Sequential sequence numbers (1, 2, 3...)
- Consistent ID number (1234)
- **Assessment**: Normal network connectivity test

### ICMP Flood Attack Pattern

**Malicious Traffic**:
```
17:05:00.000001 IP 10.1.1.1 > 192.168.1.100: ICMP echo request, length 1000
17:05:00.000002 IP 10.1.1.2 > 192.168.1.100: ICMP echo request, length 1000
17:05:00.000003 IP 10.1.1.3 > 192.168.1.100: ICMP echo request, length 1000
... (thousands more in rapid succession)
```

**Red Flags**:
- Extremely high packet rate (thousands per second)
- Large packet sizes (1000 bytes vs. normal 64 bytes)
- Different source IPs (likely spoofed)
- No echo replies (server overwhelmed)
- **Assessment**: ICMP flood DDoS attack!

---

## Part 6: HTTP vs. HTTPS Traffic Analysis

### HTTP (Insecure) Traffic

**Command**:
```bash
sudo tcpdump -i eth0 -A 'tcp port 80 and (tcp[((tcp[12:1] & 0xf0) >> 2):4] = 0x47455420)'
```

**What This Captures**: HTTP GET requests

**Sample Output**:
```
18:10:15.123456 IP 192.168.1.10.54321 > 93.184.216.34.80: Flags [P.], length 178
GET /login.php HTTP/1.1
Host: insecure-site.com
User-Agent: Mozilla/5.0
Cookie: sessionid=abc123
Content-Type: application/x-www-form-urlencoded

username=admin&password=Password123
```

**Security Analysis**:
- ❌ **CRITICAL**: Username and password transmitted in PLAINTEXT
- ❌ **CRITICAL**: Session cookie visible in plaintext
- ❌ Anyone on the network can intercept these credentials
- ❌ No encryption protection whatsoever
- **Recommendation**: MUST use HTTPS for any sensitive data!

### HTTPS (Secure) Traffic

**Command**:
```bash
sudo tcpdump -i eth0 'tcp port 443' -n -c 20
```

**Sample Output**:
```
18:15:20.123456 IP 192.168.1.10.54322 > 93.184.216.34.443: Flags [P.], length 517
......T....]..k.1..F.m...!.H.1..........3.....h2.http/1.1...
...........5........./.../1...+./.,.0.......
```

**Security Analysis**:
- ✅ **SECURE**: Data is encrypted (unreadable gibberish)
- ✅ TLS/SSL encryption protects confidentiality
- ✅ Even if intercepted, attacker cannot read contents
- ✅ Only metadata visible (source/dest IPs, packet size)
- **Assessment**: Secure communication - credentials and data protected

---

## Part 7: Identifying Common Network Attacks

### 1. Port Scanning Detection

**Pattern in tcpdump**:
```
19:00:00.001 IP 10.0.0.50.12345 > 192.168.1.100.21: Flags [S]
19:00:00.002 IP 10.0.0.50.12346 > 192.168.1.100.22: Flags [S]
19:00:00.003 IP 10.0.0.50.12347 > 192.168.1.100.23: Flags [S]
19:00:00.004 IP 10.0.0.50.12348 > 192.168.1.100.25: Flags [S]
... (scanning through all ports 1-65535)
```

**Indicators**:
- Same source IP scanning many destination ports
- Sequential port numbers being probed
- SYN packets without completing handshake
- **Attack Type**: Port scan (reconnaissance before attack)

### 2. ARP Spoofing Detection

**Pattern**:
```
19:10:00.123 ARP, Reply 192.168.1.1 is-at aa:bb:cc:dd:ee:ff
19:10:01.123 ARP, Reply 192.168.1.1 is-at 11:22:33:44:55:66  # Different MAC!
```

**Indicators**:
- Same IP address associated with different MAC addresses
- Unsolicited ARP replies
- **Attack Type**: ARP spoofing (man-in-the-middle attack setup)

### 3. Unusual Outbound Connections

**Pattern**:
```
19:15:00.123 IP 192.168.1.42 > 185.220.101.5.8443: Flags [S]  # To unknown foreign IP
19:15:00.145 IP 185.220.101.5.8443 > 192.168.1.42: Flags [S.]
19:15:00.146 IP 192.168.1.42 > 185.220.101.5.8443: Flags [.]
19:15:05.123 IP 192.168.1.42 > 185.220.101.5.8443: Flags [P.], length 150  # Regular beacons
19:15:65.123 IP 192.168.1.42 > 185.220.101.5.8443: Flags [P.], length 150  # Every 60 sec
```

**Indicators**:
- Connection to unusual foreign IP
- Regular beacon traffic (every 60 seconds)
- Non-standard port (8443)
- **Assessment**: Possible malware C2 (command & control) communication!

---

## Summary: Network Analysis Best Practices

### Tools and Techniques

1. **tcpdump**: Command-line packet capture (Linux/Unix)
2. **Wireshark**: GUI packet analyzer (detailed protocol analysis)
3. **tshark**: Wireshark's command-line version
4. **NetworkMiner**: Passive network forensics tool

### Key Protocols to Monitor

| Protocol | Port | Purpose | Security Concerns |
|----------|------|---------|-------------------|
| HTTP | 80 | Web traffic | ❌ Unencrypted - credentials visible |
| HTTPS | 443 | Secure web | ✅ Encrypted - secure |
| DNS | 53 | Name resolution | ⚠️ Can be used for data exfiltration |
| FTP | 21 | File transfer | ❌ Unencrypted - avoid |
| SSH | 22 | Secure shell | ✅ Encrypted remote access |
| Telnet | 23 | Remote access | ❌ Unencrypted - never use |
| SMTP | 25 | Email | ⚠️ Can be encrypted with TLS |
| RDP | 3389 | Remote desktop | ⚠️ Frequent attack target |
| SMB | 445 | File sharing | ⚠️ Vulnerable to ransomware |

### Security Analysis Checklist

When analyzing network traffic, look for:

**Indicators of Compromise (IOCs)**:
- ✅ Connections to known malicious IPs
- ✅ Unusual outbound traffic patterns
- ✅ Failed login attempts (brute force)
- ✅ Large data transfers to external IPs
- ✅ Traffic on unexpected ports
- ✅ Unencrypted transmission of credentials

**Normal vs. Abnormal Patterns**:
- ✅ Baseline normal traffic volume/patterns
- ✅ Sudden spikes in traffic (possible attack)
- ✅ Communication with unusual countries
- ✅ Traffic during off-hours
- ✅ Protocols used incorrectly

---

## Conclusion

Network layer communication analysis is a fundamental skill for security analysts. By understanding how protocols work at different layers and being able to capture and analyze packets, analysts can:

1. **Detect Security Threats**: Identify attacks like DDoS, port scans, malware C2
2. **Investigate Incidents**: Determine what happened during security breach
3. **Validate Security Controls**: Verify firewalls and security tools working correctly
4. **Understand Network Behavior**: Establish baselines for anomaly detection
5. **Ensure Compliance**: Verify encrypted protocols used for sensitive data

**Key Takeaways**:
- Always use encrypted protocols (HTTPS, SSH, SFTP) for sensitive data
- Monitor DNS traffic for exfiltration and C2 activity
- Understand TCP handshake to identify connection anomalies
- Regular packet captures help establish network baseline
- Combine packet analysis with SIEM for comprehensive security monitoring

---

**Analysis Performed By**: Luís Barros
**Date**: September 14, 2024
**Tools**: tcpdump, Wireshark
**Classification**: Internal Use Only
**Next Review**: December 14, 2024
