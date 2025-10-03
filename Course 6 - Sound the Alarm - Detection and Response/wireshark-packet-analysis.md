# Wireshark Packet Analysis - Network Traffic Investigation

## Project Description

This project demonstrates my ability to analyze network traffic using Wireshark to investigate security incidents and identify suspicious activity. I performed packet capture analysis on various network scenarios, including identifying malicious traffic patterns, analyzing protocol behavior, and extracting indicators of compromise. This showcases practical skills in network forensics and traffic analysis essential for incident detection and response.

---

## Scenario 1: Analyzing DNS Exfiltration

### Objective
Investigate suspicious DNS traffic that may indicate data exfiltration.

### Background
Security monitoring detected an unusually high volume of DNS queries from a workstation. DNS tunneling is a technique attackers use to exfiltrate data or establish command and control channels by encoding data in DNS queries.

### Packet Capture Analysis

#### Initial Observations
- **Total Packets Captured**: 2,847
- **Time Span**: 15 minutes
- **Protocol Distribution**:
  - DNS: 92% (unusual - normally <5%)
  - HTTP/HTTPS: 5%
  - Other: 3%

#### Wireshark Display Filters Used

```
dns
```
This basic filter showed all DNS traffic, immediately revealing the abnormal volume.

```
dns.qry.name contains ".malicious-domain.com"
```
Filtered for specific suspicious domain found in initial review.

```
dns and ip.src == 192.168.1.105
```
Isolated traffic from the suspected compromised workstation.

#### Key Findings

**Suspicious DNS Queries Identified:**

```
Standard DNS Query:
www.example.com A (IPv4 address)

Suspicious Queries Found:
ZGF0YS1leGZpbHRyYXRpb24.malicious-domain.com A
aW1wb3J0YW50LWRvY3VtZW50.malicious-domain.com A
Y29uZmlkZW50aWFsLWZpbGU.malicious-domain.com A
```

**Analysis:**
- Subdomain prefixes appear to be Base64-encoded data
- Query rate: 150+ queries per minute (normal: 5-10/minute)
- All queries to same suspicious domain
- No legitimate business purpose for these queries

**Decoded Subdomains** (Base64):
- `ZGF0YS1leGZpbHRyYXRpb24` = "data-exfiltration"
- `aW1wb3J0YW50LWRvY3VtZW50` = "important-document"
- `Y29uZmlkZW50aWFsLWZpbGU` = "confidential-file"

#### Packet Details Examined

**Example DNS Query Packet:**

```
Frame 157: 89 bytes on wire (712 bits)
Ethernet II, Src: Dell_a3:b2:c1, Dst: Cisco_d4:e5:f6
Internet Protocol Version 4, Src: 192.168.1.105, Dst: 8.8.8.8
User Datagram Protocol, Src Port: 54321, Dst Port: 53
Domain Name System (query)
    Transaction ID: 0x1a2b
    Flags: 0x0100 Standard query
    Questions: 1
        Name: ZGF0YS1leGZpbHRyYXRpb24.malicious-domain.com
        Type: A (Host Address)
        Class: IN (0x0001)
```

**Key Indicators:**
- Source: Internal workstation (192.168.1.105)
- Destination: Google DNS (8.8.8.8) - bypassing internal DNS
- Encoded data in subdomain portion
- High query frequency

### Conclusions

**Threat Identified**: DNS Tunneling / Data Exfiltration
**Severity**: CRITICAL
**Compromised Host**: 192.168.1.105 (DESKTOP-FINANCE-07)

**Recommendations**:
1. Immediately isolate compromised workstation
2. Block malicious-domain.com at firewall
3. Force all DNS queries through internal DNS (prevent DNS bypass)
4. Implement DNS monitoring and anomaly detection
5. Conduct forensic analysis of compromised workstation
6. Review data accessed by affected user account

---

## Scenario 2: HTTP Traffic Analysis - Credential Theft

### Objective
Analyze HTTP traffic to identify potential credential theft over unencrypted connections.

### Background
A user reported their account was compromised shortly after accessing a company web application from a coffee shop WiFi. Investigate packet capture to determine if credentials were transmitted insecurely.

### Wireshark Display Filters Used

```
http
```
Basic filter to show all HTTP traffic (unencrypted).

```
http.request.method == "POST"
```
Filter for POST requests, commonly used for login forms.

```
http contains "password"
```
Search packet payload for literal string "password".

```
http.request.uri contains "login"
```
Focus on login-related requests.

### Packet Capture Analysis

#### HTTP POST Request to Login Page

**Packet Details:**

```
Frame 423: 387 bytes on wire
Ethernet II
Internet Protocol Version 4, Src: 192.168.1.87, Dst: 203.0.113.50
Transmission Control Protocol, Src Port: 49876, Dst Port: 80
Hypertext Transfer Protocol
    POST /login.php HTTP/1.1
    Host: webapp.company.com
    User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64)
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 47

    HTML Form URL Encoded: application/x-www-form-urlencoded
        Form item: "username" = "jsmith@company.com"
        Form item: "password" = "P@ssw0rd123"
        Form item: "submit" = "Login"
```

**Critical Finding:**
- Username and password transmitted in **PLAINTEXT** over HTTP (not HTTPS)
- Credentials fully visible in packet capture: `jsmith@company.com / P@ssw0rd123`
- Anyone on the same network (coffee shop WiFi) could capture this traffic
- No encryption protection whatsoever

#### Follow HTTP Stream

Using Wireshark's "Follow HTTP Stream" feature (right-click packet → Follow → HTTP Stream):

```
POST /login.php HTTP/1.1
Host: webapp.company.com
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64)
Content-Type: application/x-www-form-urlencoded
Content-Length: 47

username=jsmith%40company.com&password=P%40ssw0rd123&submit=Login

HTTP/1.1 302 Found
Location: /dashboard.php
Set-Cookie: PHPSESSID=a1b2c3d4e5f6g7h8i9j0
```

**Additional Findings:**
- Session cookie also transmitted over HTTP (can be stolen)
- No warning to user about insecure connection
- Application redirects to dashboard after login (successful authentication)

### Conclusions

**Threat Identified**: Credential Theft via Unencrypted HTTP
**Severity**: HIGH
**Root Cause**: Web application not enforcing HTTPS/TLS

**Impact**:
- User credentials captured by attacker on coffee shop WiFi
- Session cookie also compromised
- Account accessed by unauthorized party

**Recommendations**:
1. **IMMEDIATE**: Force password reset for jsmith@company.com
2. **IMMEDIATE**: Review account activity for unauthorized actions
3. **CRITICAL**: Enforce HTTPS for all web applications
4. **HIGH**: Implement HTTP Strict Transport Security (HSTS)
5. **HIGH**: Deploy VPN for remote access to internal applications
6. **MEDIUM**: Security awareness training on public WiFi risks
7. **MEDIUM**: Implement certificate pinning for mobile apps

---

## Scenario 3: Malware Communication - C2 Traffic

### Objective
Identify command and control (C2) traffic from suspected malware infection.

### Background
Endpoint detection system flagged suspicious outbound connections from a workstation. Packet capture was taken to analyze the nature of the communication and identify indicators of compromise.

### Wireshark Display Filters Used

```
ip.dst == 185.220.101.5
```
Filter for traffic to suspicious external IP address.

```
tcp.port == 8443 or tcp.port == 443
```
Focus on common C2 ports.

```
http.request or tls.handshake
```
Examine protocol-level details.

```
tcp.flags.syn == 1 and tcp.flags.ack == 0
```
Show connection initiation attempts (SYN packets).

### Packet Capture Analysis

#### Suspicious Outbound Connection Pattern

**Observed Behavior:**
```
Time        Source IP       Destination IP   Protocol  Info
10:15:01    192.168.1.42    185.220.101.5   TCP       49234 → 8443 [SYN]
10:15:01    185.220.101.5   192.168.1.42    TCP       8443 → 49234 [SYN, ACK]
10:15:01    192.168.1.42    185.220.101.5   TCP       49234 → 8443 [ACK]
10:15:02    192.168.1.42    185.220.101.5   TLSv1.2   Client Hello
10:15:02    185.220.101.5   192.168.1.42    TLSv1.2   Server Hello
10:15:03    192.168.1.42    185.220.101.5   TLS       Application Data
```

**Pattern Characteristics:**
- Connection repeats every 60 seconds (beacon interval)
- Always to same external IP (185.220.101.5:8443)
- Uses TLS encryption (hides C2 commands)
- Small data transfers (50-200 bytes)
- Regular timing (not user-generated traffic)

#### TLS Certificate Analysis

**Server Certificate Details** (from TLS handshake):

```
Transport Layer Security
    TLSv1.2 Record Layer: Handshake Protocol: Certificate
        Certificate: 308203...
            signedCertificate
                serialNumber: 0x1a2b3c4d5e6f
                signature (sha256WithRSAEncryption)
                issuer: CN=Self-Signed CA
                validity
                    notBefore: 2024-01-01 00:00:00 UTC
                    notAfter: 2025-01-01 00:00:00 UTC
                subject: CN=update-server.com
                subjectPublicKeyInfo
```

**Red Flags:**
- Self-signed certificate (not from trusted CA)
- Generic subject name ("update-server.com")
- Recently created certificate (January 2024)
- Certificate issuer and subject are same (self-signed)

#### Beacon Analysis

**Beacon Timing:**
```
Packet #  Time          Time Delta
142       10:15:01      (baseline)
187       10:16:01      60.000 seconds
243       10:17:01      60.001 seconds
289       10:18:01      59.999 seconds
```

**Statistical Analysis:**
- Mean beacon interval: 60.0 seconds
- Standard deviation: 0.002 seconds
- Extremely consistent timing (hallmark of automated malware)

#### Traffic Volume Analysis

Using Wireshark Statistics → Conversations:

```
Address A           Address B           Packets  Bytes    Duration
192.168.1.42        185.220.101.5       847      156,234  3600s (1 hour)
```

**Analysis:**
- Continuous communication for entire capture duration
- Small packet sizes (avg 184 bytes) - consistent with C2 beaconing
- No legitimate business purpose for this connection

### Conclusions

**Threat Identified**: Malware C2 Communication (Beacon Traffic)
**Severity**: CRITICAL
**Compromised Host**: 192.168.1.42 (LAPTOP-SALES-11)
**C2 Server**: 185.220.101.5:8443

**Indicators of Compromise (IoCs):**
- C2 IP: 185.220.101.5
- C2 Port: 8443/TCP
- Beacon Interval: 60 seconds
- Certificate CN: update-server.com
- Certificate Serial: 0x1a2b3c4d5e6f

**Malware Characteristics:**
- Persistent C2 communication
- TLS-encrypted traffic (evade inspection)
- Regular beaconing pattern
- Self-signed certificate for C2

**Recommendations**:
1. **IMMEDIATE**: Isolate infected workstation from network
2. **IMMEDIATE**: Block C2 IP (185.220.101.5) at firewall
3. **HIGH**: Conduct forensic analysis of infected system
4. **HIGH**: Scan network for additional infected hosts (IOC sweep)
5. **HIGH**: Review data accessed by compromised system
6. **HIGH**: Implement TLS inspection on firewall (decrypt and inspect HTTPS)
7. **MEDIUM**: Deploy network IDS/IPS with C2 detection signatures
8. **MEDIUM**: Implement DNS sinkholing for known C2 domains

---

## Wireshark Skills Demonstrated

### Technical Skills

1. **Packet Capture Analysis**
   - Captured and analyzed thousands of packets
   - Identified anomalous traffic patterns
   - Extracted IOCs from network traffic

2. **Display Filter Proficiency**
   - Protocol filters (dns, http, tls)
   - IP and port filters
   - Content matching (contains)
   - Logical operators (and, or)
   - TCP flag analysis

3. **Protocol Analysis**
   - DNS protocol structure and queries
   - HTTP headers and form data
   - TLS handshake and certificate inspection
   - TCP connection establishment (3-way handshake)

4. **Traffic Pattern Recognition**
   - Beacon detection (timing analysis)
   - Data exfiltration indicators
   - Credential theft in cleartext
   - C2 communication patterns

5. **Forensic Techniques**
   - Following streams (TCP/HTTP)
   - Certificate analysis
   - Statistical traffic analysis
   - Timeline reconstruction

### Analytical Skills

- Identified three distinct attack scenarios
- Determined severity and business impact
- Extracted actionable IOCs
- Provided prioritized remediation recommendations
- Correlated technical findings with business risk

### Tools Used

- **Wireshark**: Primary packet analysis tool
- **Display Filters**: Advanced traffic filtering
- **Statistics Features**: Conversation analysis, protocol hierarchy
- **Follow Stream**: Application-layer data extraction
- **Export Objects**: File extraction from traffic

---

## Summary

Through these three scenarios, I demonstrated comprehensive network traffic analysis skills using Wireshark:

1. **DNS Exfiltration**: Identified data exfiltration through DNS tunneling by analyzing query patterns and decoding Base64-encoded subdomains

2. **Credential Theft**: Discovered plaintext credential transmission over HTTP, highlighting critical application security vulnerability

3. **C2 Communication**: Detected malware beacon traffic through timing analysis, certificate inspection, and pattern recognition

### Key Takeaways

**Security Implications:**
- Network traffic analysis is essential for detecting threats that bypass endpoint controls
- Protocol analysis reveals attack techniques (exfiltration, credential theft, C2)
- Unencrypted protocols (HTTP) expose sensitive data
- Even encrypted traffic (TLS) can reveal IOCs through metadata analysis

**Defensive Measures:**
- Implement TLS/HTTPS for all applications
- Monitor DNS traffic for anomalies
- Deploy network IDS/IPS for protocol-level inspection
- Use network segmentation and egress filtering
- Implement SSL/TLS inspection for outbound traffic
- Regular packet capture analysis for threat hunting

These packet analysis investigations showcase practical skills in network forensics, incident detection, and security monitoring essential for security operations center (SOC) and incident response roles.

---

**Analysis Performed By**: [Your Name], Security Analyst
**Date**: [Current Date]
**Tools**: Wireshark 4.0.x
**Environment**: Security Operations / Incident Response
