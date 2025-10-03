# Home Network Asset Classification

## Project Description

This activity demonstrates my ability to identify, classify, and document assets in a network environment. I conducted an assessment of a home network to catalog all connected devices and determine their appropriate classification levels based on sensitivity and importance. This exercise showcases asset management skills critical for implementing security controls and risk management strategies.

---

## Asset Inventory

### Network Infrastructure

#### 1. Wireless Router/Access Point
- **Asset Type**: Network Infrastructure
- **Description**: TP-Link Archer AX3000 WiFi 6 Router
- **Function**: Provides internet connectivity, WiFi access, and network gateway services
- **Classification**: CRITICAL
- **Justification**: Single point of failure for all network connectivity; compromise would affect all devices and expose traffic
- **Security Controls**:
  - Strong WPA3 password
  - Firmware kept updated
  - Default admin credentials changed
  - Guest network isolated from main network
  - UPnP disabled

---

#### 2. Network Switch
- **Asset Type**: Network Infrastructure
- **Description**: 8-port Gigabit Ethernet switch
- **Function**: Provides wired network connectivity for multiple devices
- **Classification**: HIGH
- **Justification**: Critical for wired device connectivity; compromise could enable traffic sniffing
- **Security Controls**:
  - Physical security (locked in cabinet)
  - MAC address filtering
  - Port security enabled

---

### Computing Devices

#### 3. Desktop Computer (Primary)
- **Asset Type**: Endpoint Device
- **Description**: Windows 11 Pro desktop workstation
- **Function**: Primary work computer; stores personal and financial documents
- **Data Stored**:
  - Personal documents
  - Tax records and financial spreadsheets
  - Family photos and videos
  - Work-from-home files (if applicable)
- **Classification**: CRITICAL
- **Justification**: Contains sensitive personal and financial information; compromise would result in significant data loss and privacy breach
- **Security Controls**:
  - BitLocker full-disk encryption
  - Windows Defender Antivirus (active and updated)
  - Automatic Windows updates
  - Standard user account (not admin for daily use)
  - Strong password with Windows Hello biometric
  - Automatic screen lock (5 minutes)
  - Regular backups to external drive and cloud

---

#### 4. Laptop (Personal)
- **Asset Type**: Endpoint Device
- **Description**: MacBook Pro 2021
- **Function**: Portable computing for browsing, email, streaming
- **Data Stored**:
  - Email archives
  - Browsing history
  - Saved passwords (in keychain)
  - Personal documents
- **Classification**: HIGH
- **Justification**: Mobile device with risk of loss/theft; contains personal data and saved credentials
- **Security Controls**:
  - FileVault disk encryption
  - Strong password with Touch ID
  - Find My Mac enabled
  - Automatic macOS updates
  - Screen lock when lid closed
  - iCloud backup

---

#### 5. Smartphone (Primary)
- **Asset Type**: Mobile Device
- **Description**: iPhone 14 Pro
- **Function**: Communications, banking apps, two-factor authentication
- **Data Stored**:
  - Contact information
  - Text messages and call logs
  - Banking app access
  - Email access
  - Photos and videos
  - 2FA authentication codes
- **Classification**: CRITICAL
- **Justification**: Contains highly sensitive data; used for financial transactions and 2FA; high risk of loss or theft
- **Security Controls**:
  - Strong passcode (8-digit) with Face ID
  - iOS encryption (enabled by default)
  - Find My iPhone enabled with remote wipe capability
  - Automatic iOS updates
  - Banking apps with additional PIN/biometric
  - No jailbreak
  - Regular iCloud backups

---

#### 6. Tablet
- **Asset Type**: Mobile Device
- **Description**: iPad Air
- **Function**: Media consumption, casual browsing, reading
- **Data Stored**:
  - Downloaded books and movies
  - Browsing history
  - Some email access
- **Classification**: MEDIUM
- **Justification**: Limited sensitive data; primarily used for consumption; still capable of network access
- **Security Controls**:
  - 6-digit passcode with Touch ID
  - Automatic iPadOS updates
  - Find My iPad enabled
  - Separate Apple ID (or restrictions)

---

### Smart Home Devices

#### 7. Smart TV
- **Asset Type**: IoT Device
- **Description**: Samsung 65" Smart TV
- **Function**: Streaming services, media playback
- **Data Stored**:
  - Streaming service credentials (cached)
  - Viewing history
  - WiFi credentials
- **Classification**: LOW
- **Justification**: Limited sensitive data; primarily entertainment device; potential privacy concern with camera/microphone
- **Security Controls**:
  - Camera and microphone disabled
  - Automatic firmware updates enabled
  - Streaming apps use strong unique passwords
  - Connected to guest WiFi (isolated network)

---

#### 8. Smart Home Hub (Amazon Echo)
- **Asset Type**: IoT Device
- **Description**: Amazon Echo Dot (4th Gen)
- **Function**: Voice assistant, smart home control
- **Data Stored**:
  - Voice recordings (stored in cloud)
  - Connected account information
  - Smart home device credentials
- **Classification**: MEDIUM
- **Justification**: Always-listening device; privacy concerns; can control other smart devices; compromise could expose home automation
- **Security Controls**:
  - Amazon account with 2FA
  - Voice purchase PIN required
  - Regular voice history deletion
  - Mute button used when privacy needed
  - Connected to guest WiFi

---

#### 9. Smart Thermostat
- **Asset Type**: IoT Device
- **Description**: Nest Learning Thermostat
- **Function**: Climate control and automation
- **Data Stored**:
  - Home/away patterns
  - Temperature preferences
  - WiFi credentials
  - Google account linkage
- **Classification**: MEDIUM
- **Justification**: Reveals occupancy patterns; compromise could affect home comfort or signal when home is empty
- **Security Controls**:
  - Google account with 2FA
  - Strong WiFi password
  - Firmware updates automatic
  - Connected to main network (requires access for climate control)

---

#### 10. Smart Door Lock
- **Asset Type**: IoT Device / Physical Security
- **Description**: August Smart Lock Pro
- **Function**: Keyless entry, remote lock control
- **Data Stored**:
  - Entry/exit logs
  - User access codes
  - Mobile device IDs
- **Classification**: HIGH
- **Justification**: Physical security device; compromise could allow unauthorized physical access to home
- **Security Controls**:
  - Strong unique access codes for each user
  - App account with 2FA
  - Activity log monitoring
  - Auto-lock after 30 seconds
  - Firmware kept updated
  - Battery backup
  - Physical key backup

---

### Security and Monitoring

#### 11. Security Camera System
- **Asset Type**: Security Device / IoT
- **Description**: Ring Video Doorbell + 2 Ring Spotlight Cameras
- **Function**: Video surveillance, motion detection, visitor monitoring
- **Data Stored**:
  - Video recordings (cloud storage)
  - Motion event logs
  - Visitor history
- **Classification**: HIGH
- **Justification**: Surveillance footage is sensitive; compromise could reveal home security setup and expose privacy
- **Security Controls**:
  - Ring account with 2FA
  - Strong unique password
  - Video encryption (in transit and at rest)
  - Motion detection zones configured
  - Privacy zones to exclude neighbors
  - Regular review of shared access
  - Connected to guest WiFi

---

### Storage and Backup

#### 12. Network Attached Storage (NAS)
- **Asset Type**: Storage Device
- **Description**: Synology DS220+ 2-bay NAS (8TB total)
- **Function**: Central file storage, media server, backup target
- **Data Stored**:
  - Complete family photo/video archive
  - Document backups
  - Media library
  - System backups from computers
- **Classification**: CRITICAL
- **Justification**: Single repository for most important family data; loss would be catastrophic; compromise would expose all backed-up data
- **Security Controls**:
  - Strong admin password
  - Non-default admin username
  - 2FA for admin access
  - Encrypted shared folders
  - Automatic Synology DSM updates
  - Firewall rules (no internet exposure)
  - RAID 1 mirroring for redundancy
  - Regular backup to cloud (encrypted)
  - Restricted to wired connection only

---

#### 13. External Backup Drive
- **Asset Type**: Storage Device
- **Description**: Western Digital 4TB USB external hard drive
- **Function**: Offline backup of critical data
- **Data Stored**:
  - Weekly backup of desktop files
  - Financial document archives
  - System images
- **Classification**: CRITICAL
- **Justification**: Contains complete backups of sensitive data; theft would expose all backed-up information
- **Security Controls**:
  - Hardware-encrypted drive (password protected)
  - Stored offline (disconnected when not backing up)
  - Physical security (locked drawer)
  - Backup software with encryption
  - Regular backup verification

---

### Gaming and Entertainment

#### 14. Gaming Console
- **Asset Type**: Entertainment Device
- **Description**: PlayStation 5
- **Function**: Gaming, media streaming
- **Data Stored**:
  - PlayStation Network account
  - Saved credit card information
  - Game save data
- **Classification**: MEDIUM
- **Justification**: Contains payment information; PSN account compromise could lead to unauthorized purchases
- **Security Controls**:
  - Strong PSN password
  - 2FA enabled on PSN account
  - Parental controls configured
  - Spending limits set
  - Connected to guest WiFi

---

#### 15. Streaming Device
- **Asset Type**: Entertainment Device
- **Description**: Roku Streaming Stick+
- **Function**: Streaming media content
- **Data Stored**:
  - Streaming service credentials
  - Viewing history
  - WiFi credentials
- **Classification**: LOW
- **Justification**: Minimal sensitive data; primarily entertainment
- **Security Controls**:
  - Roku account with strong password
  - PIN for purchases
  - Automatic updates
  - Connected to guest WiFi

---

### Printers and Peripherals

#### 16. Network Printer
- **Asset Type**: Peripheral Device
- **Description**: HP LaserJet Pro MFP (Print/Scan/Copy)
- **Function**: Document printing and scanning
- **Data Stored**:
  - Print job history
  - Scanned document cache
  - WiFi credentials
  - Email settings (for scan-to-email)
- **Classification**: MEDIUM
- **Justification**: Print jobs may contain sensitive documents; old print jobs stored in memory; network device that could be attack vector
- **Security Controls**:
  - Default admin password changed
  - Firmware updated regularly
  - Print job logging cleared regularly
  - Hard drive encryption enabled (if available)
  - Scan jobs deleted after completion
  - Secure Print feature (PIN required)

---

## Asset Classification Summary

| Classification | Count | Assets |
|----------------|-------|--------|
| **CRITICAL** | 4 | Desktop Computer, Smartphone, NAS, External Backup Drive |
| **HIGH** | 3 | Laptop, Smart Door Lock, Security Cameras |
| **MEDIUM** | 5 | Tablet, Smart Home Hub, Smart Thermostat, Gaming Console, Network Printer |
| **LOW** | 2 | Smart TV, Streaming Device |
| **Infrastructure** | 2 | Wireless Router (Critical), Network Switch (High) |

**Total Assets**: 16 devices

---

## Network Diagram

```
Internet
   |
[Modem/ISP]
   |
[Wireless Router] ---- [Network Switch]
   |                          |
   |                          |
Main WiFi              Wired Devices:
   |                    - Desktop Computer
   |                    - NAS
   |
Protected Devices:
- Laptop
- Smartphone
- Tablet
- Smart Thermostat
- Smart Door Lock
- Network Printer


Guest WiFi (Isolated)
   |
IoT Devices:
- Smart TV
- Smart Home Hub
- Security Cameras
- Gaming Console
- Streaming Device
```

---

## Risk Assessment by Asset

### Critical Assets - Primary Security Focus

1. **Smartphone**: Highest risk due to mobility, theft potential, and concentration of sensitive apps
2. **Desktop Computer**: High value target due to stored financial and personal data
3. **NAS**: Single point of failure for family data; attractive target for ransomware
4. **External Backup**: Offline security critical to prevent simultaneous compromise with NAS

### High-Risk Scenarios

- **Smartphone loss/theft**: Immediate remote wipe required; 2FA codes compromised
- **Router compromise**: All network traffic exposed; man-in-the-middle attacks possible
- **NAS ransomware**: Family data encrypted; ransom demanded
- **Smart lock hacking**: Unauthorized physical access to home

---

## Data Protection Controls

### Implemented Controls

✅ Encryption at rest (desktop, laptop, NAS, external drive, mobile devices)
✅ Strong unique passwords on all devices
✅ Multi-factor authentication on critical accounts
✅ Network segmentation (main vs. guest WiFi)
✅ Regular backups (3-2-1 strategy)
✅ Automatic security updates
✅ Physical security for critical devices
✅ Least privilege (non-admin daily use accounts)

### Recommended Additional Controls

- [ ] VLAN segmentation for IoT devices
- [ ] Network IDS/IPS (e.g., Firewalla, Ubiquiti Dream Machine)
- [ ] Regular security audits of smart home devices
- [ ] Password manager for family
- [ ] VPN for remote access when away from home
- [ ] Network traffic monitoring
- [ ] Documented incident response plan
- [ ] Regular backup restoration tests

---

## Lessons Learned

This asset inventory exercise demonstrated:

1. **Asset Sprawl**: Modern homes contain many more networked devices than commonly realized (16 in this example)

2. **IoT Security Challenges**: Smart home devices often have weaker security and require network isolation

3. **Classification Importance**: Understanding asset criticality helps prioritize security controls and budget

4. **Backup Critical**: Multiple backup layers (NAS + external drive + cloud) essential for critical data

5. **Network Segmentation**: Guest WiFi for IoT devices reduces risk from compromised smart devices

6. **Update Management**: Keeping firmware current across many devices is challenging but essential

7. **Physical Security**: Even network devices require physical security considerations

---

## Summary

This home network asset classification project identified and categorized 16 connected devices across five classification levels. The exercise revealed:

- **4 CRITICAL assets** requiring maximum security controls (encryption, 2FA, regular backups)
- **3 HIGH priority assets** with enhanced security measures
- **5 MEDIUM priority assets** with standard security controls
- **2 LOW priority assets** with basic protections
- **2 infrastructure components** that are force multipliers for security

The analysis demonstrates my ability to:
- Identify and catalog network assets systematically
- Assess asset criticality based on data sensitivity and business impact
- Apply appropriate security controls proportional to asset classification
- Recognize IoT-specific security challenges
- Design network segmentation strategies
- Implement defense-in-depth principles

These asset management skills are directly applicable to enterprise environments where proper asset inventory, classification, and control implementation are fundamental to effective cybersecurity programs.

---

**Assessment Completed By**: [Your Name]
**Date**: [Current Date]
**Review Frequency**: Quarterly
**Next Review**: [Date + 3 months]
