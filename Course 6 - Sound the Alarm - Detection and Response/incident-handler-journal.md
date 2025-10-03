# Incident Handler's Journal

## Overview

This journal documents security incidents investigated and responded to during my cybersecurity training. Each entry follows a structured format to ensure thorough documentation and facilitate incident analysis. The journal demonstrates my ability to identify, analyze, and respond to various types of security incidents using industry-standard frameworks and tools.

---

## Entry #1: Ransomware Attack - Healthcare Clinic

**Date**: September 15, 2024
**Entry Number**: 001
**Incident Type**: Ransomware Attack

### Description

A small U.S. healthcare clinic experienced a ransomware attack that encrypted critical patient files and systems. Employees reported they could not access electronic health records (EHR), billing systems, or patient scheduling software. A ransom note appeared on multiple workstations demanding Bitcoin payment for decryption keys. The ransom note indicated the attack was delivered via a phishing email that an employee opened, which contained a malicious attachment.

### Tools Used

- **SIEM**: Splunk Enterprise for log analysis
- **Endpoint Protection**: CrowdStrike Falcon EDR for endpoint investigation
- **Network Analysis**: Wireshark for packet capture analysis
- **Malware Analysis**: VirusTotal for file hash checking
- **Forensics**: FTK Imager for evidence preservation

### The 5 W's

**Who**: External threat actor (ransomware group); internal victim was a front desk staff member who opened the phishing email

**What**: Ransomware infection that encrypted patient files, EHR database, and financial records; attackers gained initial access through phishing email with malicious Microsoft Word document containing macro

**Where**: Healthcare clinic network; originated from front desk workstation; spread to file server and database server via lateral movement through network shares

**When**:
- Phishing email received: September 14, 2024, 10:23 AM
- Malicious attachment opened: September 14, 2024, 2:47 PM
- Encryption began: September 14, 2024, 2:52 PM
- Incident discovered: September 15, 2024, 7:15 AM (next morning)
- Incident reported to IT: September 15, 2024, 7:30 AM

**Why**: Financial motivation by ransomware actors; healthcare sector targeted due to critical nature of data and higher likelihood of ransom payment; weak security controls (no email filtering, lack of employee training, no network segmentation)

### NIST Incident Response Lifecycle Analysis

#### 1. Preparation
**Assessment**:
- ❌ No security awareness training program
- ❌ Email security filtering not implemented
- ❌ No endpoint detection and response (EDR)
- ✅ Incident response contact information available
- ❌ No tested backup and recovery procedures
- ❌ Network not segmented

**Gaps Identified**: Organization was inadequately prepared for ransomware attacks

#### 2. Detection and Analysis
**How Detected**:
- Users unable to access files when arriving for work
- Ransom notes visible on multiple workstations
- File extensions changed to .encrypted

**Analysis Performed**:
- Reviewed Splunk logs to identify patient zero (front desk workstation)
- Analyzed email headers to identify phishing email source
- Used VirusTotal to identify malware variant (Ryuk ransomware family)
- Examined network logs to track lateral movement
- Identified 47 affected systems (15 workstations, 2 servers, 30 shared drives)

**Indicators of Compromise (IoCs)**:
- Malicious file hash: 3a4b5c6d7e8f9g0h1i2j3k4l5m6n7o8p
- C2 server IP: 185.220.xxx.xxx
- Malicious email sender: billing@meditech-services[.]com (spoofed)
- Process name: svchosts.exe (note misspelling)

**Severity**: CRITICAL - Patient care impacted; HIPAA compliance breach

#### 3. Containment
**Short-term Containment**:
- Immediately disconnected affected systems from network (10:15 AM)
- Disabled network shares to prevent further spread
- Isolated infected workstation and servers
- Blocked C2 server IP at firewall
- Disabled user account that opened phishing email
- Notified staff to not open emails or attachments

**Long-term Containment**:
- Re-imaged all infected workstations
- Rebuilt servers from known-good backups
- Implemented temporary paper-based patient records
- Changed all passwords across organization
- Implemented email filtering rules

#### 4. Eradication
**Actions Taken**:
- Removed ransomware from all identified systems
- Scanned entire network for additional malware
- Verified no persistence mechanisms remained
- Cleaned and validated backup integrity
- Confirmed C2 communication ceased

**Verification**:
- Full network scan with updated antivirus definitions
- No additional malware detected
- Network traffic analysis showed no suspicious outbound connections

#### 5. Recovery
**Recovery Actions**:
- Restored critical systems in priority order:
  1. Authentication server (AD) - Day 1
  2. EHR database server - Day 2
  3. Billing system - Day 2
  4. Workstations - Days 2-3
  5. Non-critical systems - Days 3-4

- Restored data from backups (last backup was 5 days old - some data loss)
- Validated restored data integrity with clinic staff
- Monitored systems for 48 hours before full production use
- Returned to normal operations: September 19, 2024

**Data Loss**:
- 5 days of patient data entry lost
- Manual data re-entry required for 87 patient records
- No permanent patient data loss (paper records available)

#### 6. Post-Incident Activity (Lessons Learned)
**What Went Well**:
- Quick detection and response by staff
- Backups were available (though not recent)
- Clear chain of command for incident response
- Minimal long-term impact to patient care

**What Could Be Improved**:
- Faster backup frequency needed (daily, not weekly)
- Email security filtering required
- Security awareness training mandatory
- EDR solution needed for early detection
- Network segmentation to limit lateral movement
- Offline backup storage (ransomware encrypted backup drive)

**Recommendations Implemented**:
1. ✅ Daily automated backups with offsite/offline storage
2. ✅ Deployed email security gateway with attachment scanning
3. ✅ Mandatory quarterly security awareness training
4. ✅ Installed EDR on all endpoints
5. ✅ Implemented network segmentation (VLANs)
6. ✅ Disabled macros in Office documents by default
7. ✅ Created detailed incident response playbook for ransomware
8. ✅ Conducted tabletop exercise for future incidents

### Additional Notes

- **Law Enforcement**: FBI and HHS OCR notified of breach (HIPAA requirement)
- **Ransom Payment**: Decision made NOT to pay ransom; restored from backups
- **Patient Notification**: 1,247 patients notified of potential breach per HIPAA
- **Insurance**: Cyber insurance claim filed; covered recovery costs
- **Total Downtime**: 4 days for full recovery
- **Estimated Cost**: $125,000 (recovery, notification, legal, insurance deductible)

---

## Entry #2: Data Breach - Unauthorized Database Access

**Date**: October 3, 2024
**Entry Number**: 002
**Incident Type**: Data Breach / Unauthorized Access

### Description

Database administrator noticed unusual query activity in production customer database during routine log review. Investigation revealed unauthorized access to customer PII (personally identifiable information) over a 3-week period. Attacker had compromised a web application and used SQL injection to access backend database. Approximately 15,000 customer records potentially accessed, including names, addresses, email, phone numbers, and hashed passwords.

### Tools Used

- **SIEM**: Splunk for log correlation
- **Database Auditing**: MySQL audit logs
- **Web Application Firewall**: ModSecurity logs
- **Network Forensics**: Zeek (formerly Bro) network security monitor
- **Vulnerability Scanner**: Nessus for web app assessment

### The 5 W's

**Who**: External attacker (IP traced to TOR exit node); database administrator detected the incident

**What**: SQL injection attack against customer portal web application; unauthorized SELECT queries extracted customer data from database; no data modification or deletion detected

**Where**: Customer-facing web application (customerportal.company.com); backend MySQL database server

**When**:
- Attack window: September 12-30, 2024 (3 weeks)
- First malicious query: September 12, 2024, 2:15 AM
- Last detected activity: September 30, 2024, 11:47 PM
- Detection: October 3, 2024, 9:30 AM
- Incident declared: October 3, 2024, 10:15 AM

**Why**: Data theft for potential identity theft, credential stuffing, or dark web sale; web application had SQL injection vulnerability in search function; input validation not properly implemented

### NIST Incident Response Lifecycle Analysis

#### 1. Preparation
**Assessment**:
- ✅ Database audit logging enabled
- ✅ Incident response team established
- ❌ No web application firewall (WAF)
- ❌ No secure coding training for developers
- ❌ Vulnerability scanning not regular
- ✅ Data breach response plan documented

#### 2. Detection and Analysis
**How Detected**:
- Unusual SELECT query patterns in database audit logs
- Queries accessing full table scans of customer data
- Queries originating from web application user (not admin)

**Analysis Performed**:
- Reviewed 3 weeks of database logs (500GB of data)
- Identified 2,847 malicious queries
- Traced queries back to web application logs
- Identified SQL injection in search parameter: `search='; DROP TABLE--`
- Confirmed 15,000 unique customer records accessed
- Determined no data modification, only exfiltration

**Indicators of Compromise**:
- Attack IP: Various TOR exit nodes
- User-Agent: Python-requests/2.28.1 (automated script)
- SQL injection pattern: `' OR '1'='1' --`
- Unusual query times: 2AM-4AM (outside business hours)

**Severity**: HIGH - Significant data breach; customer PII exposed; regulatory notification required

#### 3. Containment
**Short-term Containment**:
- Immediately patched SQL injection vulnerability (11:00 AM)
- Blocked identified TOR exit nodes at firewall
- Disabled affected web application search function temporarily
- Enabled additional database access controls
- Increased logging and monitoring

**Long-term Containment**:
- Implemented web application firewall (WAF) with SQL injection rules
- Conducted full security code review of web application
- Implemented parameterized queries throughout codebase
- Added rate limiting to API endpoints

#### 4. Eradication
**Actions Taken**:
- Patched all SQL injection vulnerabilities in web application (5 total found)
- Forced password reset for all 15,000 potentially affected customers
- Revoked and regenerated all application API keys
- Scanned for additional web shells or backdoors (none found)

#### 5. Recovery
**Recovery Actions**:
- Deployed patched application to production (October 4, 2024)
- Re-enabled search functionality with input validation
- Implemented additional monitoring for SQL injection attempts
- Customer notification process initiated (October 5, 2024)
- Provided customers with credit monitoring services (1 year)

**Monitoring**:
- 30-day enhanced monitoring period
- Daily review of database access patterns
- WAF alert review

#### 6. Post-Incident Activity
**Lessons Learned**:
- Input validation is critical for all user inputs
- Code review process needs security focus
- Regular penetration testing required
- WAF should have been in place before incident

**Recommendations Implemented**:
1. ✅ Implemented WAF (Cloudflare)
2. ✅ Secure coding training for all developers
3. ✅ Quarterly penetration testing
4. ✅ Input validation library implemented
5. ✅ Parameterized queries enforced (no string concatenation)
6. ✅ Database access principle of least privilege
7. ✅ Real-time alerting for suspicious database queries

### Regulatory Notifications

- **GDPR**: 72-hour notification to EU data protection authority (completed)
- **State Laws**: California (CCPA), New York, others - customer notifications sent
- **PCI DSS**: No payment card data exposed; PCI QSA notified for awareness

### Additional Notes

- **Customer Impact**: 15,000 customers notified; 0.3% cancellation rate
- **Cost**: $285,000 (notification, credit monitoring, legal, investigation)
- **Reputation**: Negative press coverage; proactive communication helped
- **Legal**: No lawsuits filed as of date
- **Insurance**: Cyber insurance covered majority of costs

---

## Entry #3: Malware Infection - Spear Phishing

**Date**: October 22, 2024
**Entry Number**: 003
**Incident Type**: Malware / Trojan

### Description

Employee in finance department received targeted spear phishing email appearing to be from CFO requesting urgent wire transfer. Email contained attachment "Invoice_Q3_2024.pdf.exe" disguised as PDF. Employee opened attachment, which installed remote access trojan (RAT) on workstation. Malware attempted to establish C2 communication and begin data exfiltration before being detected by EDR.

### Tools Used

- **EDR**: CrowdStrike Falcon
- **SIEM**: Splunk Enterprise
- **Email Security**: Proofpoint for email analysis
- **Malware Sandbox**: ANY.RUN for behavioral analysis
- **Network Analysis**: Wireshark, tcpdump

### The 5 W's

**Who**: Finance employee (targeted victim); sophisticated threat actor (possibly APT group based on tactics)

**What**: Spear phishing email with malicious executable; RAT malware (AsyncRAT variant); attempted credential theft and data exfiltration

**Where**: Finance department workstation; email originated from spoofed domain cfo@company-corp[.]com (legitimate is company.com)

**When**:
- Email received: October 22, 2024, 8:15 AM
- Attachment opened: October 22, 2024, 8:42 AM
- Malware execution: October 22, 2024, 8:42 AM
- EDR detection: October 22, 2024, 8:43 AM (1 minute after execution)
- Incident response initiated: October 22, 2024, 8:50 AM

**Why**: Financial fraud attempt; credential and intellectual property theft; targeted attack on finance department with access to financial systems; attacker researched organization and spoofed executive

### NIST Incident Response Lifecycle Analysis

#### 1. Preparation
**Assessment**:
- ✅ EDR deployed and active (key to quick detection)
- ✅ Email security gateway with some protections
- ✅ Security awareness training (employee suspicious but still clicked)
- ✅ Incident response procedures documented
- ❌ Email authentication (DMARC/SPF/DKIM) not fully implemented

#### 2. Detection and Analysis
**How Detected**:
- CrowdStrike EDR real-time alert: "Suspicious process execution"
- Behavioral analysis detected: Process spawning PowerShell, network connections to suspicious IP, registry modifications

**Analysis Performed**:
- Isolated endpoint immediately
- Captured memory dump for forensics
- Analyzed malware behavior in sandbox environment
- Identified AsyncRAT malware variant
- Determined C2 server: 194.165.xxx.xxx (hosted in Russia)
- Confirmed no successful data exfiltration (blocked by EDR)
- Reviewed email authentication records

**Malware Capabilities** (from analysis):
- Remote access and control
- Keylogging
- Screen capture
- File upload/download
- Credential theft from browsers
- Persistence via registry run key

**Indicators of Compromise**:
- File hash: a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6
- C2 IP: 194.165.xxx.xxx:6606
- Mutex: AsyncMutex_6SI8OkPnk
- Registry key: HKCU\Software\Microsoft\Windows\CurrentVersion\Run\svchost

**Severity**: HIGH - Targeted attack; potential for significant data loss; EDR prevented worst-case scenario

#### 3. Containment
**Short-term Containment**:
- Endpoint isolated from network (automatic by EDR)
- C2 IP blocked at firewall
- Password reset for affected user
- Suspended user's access to financial systems pending investigation
- Network monitoring for additional infections (none found)

**Long-term Containment**:
- Implemented additional email authentication (DMARC, SPF, DKIM)
- Enhanced email filtering rules for executable attachments
- Blocked .exe attachments in email at gateway
- Additional monitoring of finance department

#### 4. Eradication
**Actions Taken**:
- Malware removed by EDR (automatic)
- Re-imaged workstation as precaution
- Cleared persistence mechanisms
- Verified no lateral movement to other systems
- Confirmed no other affected endpoints (full network scan)

#### 5. Recovery
**Recovery Actions**:
- Restored workstation from clean image
- Restored user data from backup
- Validated no malicious files in user's backed-up data
- User returned to work with new workstation (October 22, 2024, 2:00 PM)
- Enhanced monitoring for 14 days
- No lasting impact to operations

#### 6. Post-Incident Activity
**Lessons Learned**:
- EDR investment paid off with immediate detection and blocking
- Email security needs improvement (attachment filtering)
- User training effective (user reported feeling suspicious)
- Spear phishing remains effective attack vector

**Recommendations Implemented**:
1. ✅ Blocked executable attachments at email gateway
2. ✅ Implemented DMARC with reject policy
3. ✅ Enhanced phishing simulation training (monthly)
4. ✅ Executive impersonation alerts (warn users of external CFO/CEO emails)
5. ✅ Mandatory verbal verification for wire transfer requests
6. ✅ Finance department additional security training
7. ✅ Threat intelligence feed integration with firewall

### Additional Notes

- **Law Enforcement**: Reported to FBI (financial fraud attempt)
- **No Data Loss**: EDR prevented exfiltration
- **Response Time**: Detection to containment in 7 minutes (excellent)
- **User Outcome**: Employee received additional training; no disciplinary action (reported suspicious email, lessons learned)
- **Cost**: Minimal (staff time, re-imaging)
- **Success Story**: Demonstrates value of EDR investment

---

## Journal Summary

This incident handler's journal documents three distinct security incidents:

1. **Ransomware Attack**: Critical incident with significant business impact, demonstrating preparation gaps and recovery procedures
2. **Data Breach**: High severity incident involving SQL injection and customer data, highlighting secure coding importance
3. **Malware Infection**: High severity targeted attack successfully detected and blocked by EDR

### Key Takeaways

**Common Themes**:
- Human element remains weakest link (phishing, social engineering)
- Preparation is critical (backups, EDR, training)
- Quick detection and response minimize impact
- Lessons learned drive continuous improvement

**Skills Demonstrated**:
- NIST Incident Response Lifecycle application
- SIEM and EDR tool utilization
- Malware analysis and threat intelligence
- Forensic investigation techniques
- Regulatory compliance (HIPAA, GDPR, PCI DSS)
- Clear documentation and communication
- Risk assessment and prioritization

**Tools Proficiency**:
- Splunk SIEM
- CrowdStrike EDR
- Wireshark
- VirusTotal
- Email security analysis
- Database audit analysis
- Network forensics

---

**Journal Maintained By**: [Your Name], Security Analyst
**Organization**: Security Operations Center (SOC)
**Review Frequency**: After each incident
**Last Updated**: [Current Date]
