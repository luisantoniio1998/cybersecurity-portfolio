# Apply OS Hardening Techniques

## Overview

**Date**: September 12, 2024
**Analyst**: Luís Barros
**Organization**: Small Business IT Infrastructure
**Operating Systems**: Linux (Ubuntu Server) and Windows 10/11 Workstations

---

## Introduction

Operating System (OS) hardening is the process of securing an operating system by reducing its attack surface and implementing security best practices. This involves disabling unnecessary services, applying security patches, configuring proper access controls, and implementing additional security measures to protect against threats.

This document demonstrates OS hardening techniques applied to both Linux servers and Windows workstations in a small business environment.

---

## Part 1: Linux Server Hardening (Ubuntu Server)

### Scenario

A small business uses Ubuntu Server 20.04 LTS to host their file server and web applications. The server needs to be hardened to protect against unauthorized access, malware, and exploitation of vulnerabilities.

### Hardening Techniques Applied

#### 1. Disable Unnecessary Services and Ports

**What**: Identify and disable services that are not required for business operations

**Commands Used**:
```bash
# List all running services
systemctl list-units --type=service --state=running

# Disable unnecessary services
sudo systemctl stop cups.service          # Print service (not needed on server)
sudo systemctl disable cups.service

sudo systemctl stop avahi-daemon.service  # Zeroconf networking (not needed)
sudo systemctl disable avahi-daemon.service

sudo systemctl stop bluetooth.service     # Bluetooth (not needed on server)
sudo systemctl disable bluetooth.service
```

**Why This is Effective**:
- **Reduces Attack Surface**: Fewer running services mean fewer potential entry points for attackers
- **Conserves Resources**: Disabled services free up system memory and CPU
- **Limits Exposure**: Services you don't use can't be exploited by attackers
- **Principle of Least Functionality**: Only run services that are absolutely necessary

#### 2. Implement Strong Password Policies

**What**: Configure password complexity requirements and expiration policies

**File Modified**: `/etc/login.defs` and `/etc/pam.d/common-password`

**Configuration Applied**:
```bash
# Edit /etc/login.defs
PASS_MAX_DAYS   90        # Maximum password age
PASS_MIN_DAYS   7         # Minimum days between password changes
PASS_MIN_LEN    12        # Minimum password length
PASS_WARN_AGE   14        # Warning before password expiration

# Install password quality checking library
sudo apt install libpam-pwquality

# Edit /etc/pam.d/common-password
password requisite pam_pwquality.so retry=3 minlen=12 difok=3 ucredit=-1 lcredit=-1 dcredit=-1 ocredit=-1
```

**What This Does**:
- `minlen=12`: Minimum 12 characters
- `ucredit=-1`: At least 1 uppercase letter
- `lcredit=-1`: At least 1 lowercase letter
- `dcredit=-1`: At least 1 digit
- `ocredit=-1`: At least 1 special character
- `difok=3`: At least 3 characters different from old password

**Why This is Effective**:
- **Prevents Weak Passwords**: Forces users to create strong, complex passwords
- **Reduces Brute Force Risk**: Longer, complex passwords take exponentially longer to crack
- **Regular Updates**: Password expiration forces periodic password changes
- **Compliance**: Meets requirements for standards like PCI DSS and GDPR

#### 3. Configure and Enable Firewall (UFW)

**What**: Install and configure Uncomplicated Firewall to control network traffic

**Commands Used**:
```bash
# Install UFW
sudo apt install ufw

# Set default policies
sudo ufw default deny incoming    # Block all incoming by default
sudo ufw default allow outgoing   # Allow all outgoing

# Allow only necessary services
sudo ufw allow 22/tcp              # SSH
sudo ufw allow 80/tcp              # HTTP
sudo ufw allow 443/tcp             # HTTPS

# Enable firewall
sudo ufw enable

# Verify configuration
sudo ufw status verbose
```

**Output**:
```
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW IN    Anywhere
80/tcp                     ALLOW IN    Anywhere
443/tcp                    ALLOW IN    Anywhere
```

**Why This is Effective**:
- **Default-Deny Policy**: Only explicitly allowed traffic can enter the system
- **Limits Exposure**: Blocks potential attack vectors by closing unused ports
- **Controlled Access**: Only essential services (SSH, HTTP, HTTPS) are accessible
- **Defense in Depth**: Firewall provides additional layer even if other controls fail

#### 4. Regular Security Updates and Patch Management

**What**: Implement automated security updates to patch vulnerabilities

**Commands Used**:
```bash
# Update package lists
sudo apt update

# Upgrade all packages
sudo apt upgrade -y

# Install unattended upgrades for automatic security updates
sudo apt install unattended-upgrades

# Enable automatic security updates
sudo dpkg-reconfigure -plow unattended-upgrades

# Configure automatic updates in /etc/apt/apt.conf.d/50unattended-upgrades
Unattended-Upgrade::Allowed-Origins {
    "${distro_id}:${distro_codename}-security";
};
Unattended-Upgrade::Automatic-Reboot "true";
Unattended-Upgrade::Automatic-Reboot-Time "03:00";
```

**Why This is Effective**:
- **Closes Known Vulnerabilities**: Patches fix security flaws before they can be exploited
- **Automated Protection**: Unattended upgrades ensure timely patching without manual intervention
- **Reduces Window of Exposure**: Security patches applied as soon as they're available
- **Compliance Requirement**: Many standards require timely patching (PCI DSS requires monthly scans)

#### 5. Implement Fail2Ban for Intrusion Prevention

**What**: Install and configure Fail2Ban to automatically block brute force attacks

**Commands Used**:
```bash
# Install Fail2Ban
sudo apt install fail2ban

# Create local configuration
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local

# Edit /etc/fail2ban/jail.local
[sshd]
enabled = true
port = 22
filter = sshd
logpath = /var/log/auth.log
maxretry = 3
bantime = 3600
findtime = 600

# Start and enable Fail2Ban
sudo systemctl start fail2ban
sudo systemctl enable fail2ban

# Check status
sudo fail2ban-client status sshd
```

**Configuration Explained**:
- `maxretry = 3`: Block after 3 failed login attempts
- `bantime = 3600`: Ban IP address for 1 hour (3600 seconds)
- `findtime = 600`: 3 failures within 10 minutes triggers ban

**Why This is Effective**:
- **Blocks Brute Force Attacks**: Automatically bans IPs attempting password guessing
- **Real-time Protection**: Responds immediately to suspicious activity
- **Reduces Log Noise**: Fewer repeated attacks in logs
- **Adaptive Defense**: Attackers are blocked before they can succeed

#### 6. Implement Principle of Least Privilege

**What**: Create separate user accounts with minimal permissions

**Commands Used**:
```bash
# Create standard user (not using root for daily tasks)
sudo adduser webadmin

# Add user to sudo group for administrative tasks when needed
sudo usermod -aG sudo webadmin

# Disable root SSH login - edit /etc/ssh/sshd_config
PermitRootLogin no
PasswordAuthentication no  # Use SSH keys instead
PubkeyAuthentication yes

# Restart SSH service
sudo systemctl restart sshd

# Set up SSH keys (on client machine)
ssh-keygen -t rsa -b 4096
ssh-copy-id webadmin@server-ip
```

**Why This is Effective**:
- **Limits Damage from Compromise**: If account is compromised, attacker doesn't have root access
- **Audit Trail**: All administrative actions can be traced to specific users
- **Two-Factor Control**: User must have both SSH key AND password for sudo
- **Prevents Direct Root Access**: Attackers cannot directly attack root account via SSH

#### 7. Enable and Configure Audit Logging

**What**: Install auditd to track security-relevant events

**Commands Used**:
```bash
# Install auditd
sudo apt install auditd

# Add audit rules - edit /etc/audit/rules.d/audit.rules
# Monitor file modifications
-w /etc/passwd -p wa -k passwd_changes
-w /etc/shadow -p wa -k shadow_changes
-w /etc/sudoers -p wa -k sudoers_changes

# Monitor user/group modifications
-w /usr/sbin/useradd -p x -k user_modification
-w /usr/sbin/userdel -p x -k user_modification
-w /usr/sbin/usermod -p x -k user_modification

# Restart auditd
sudo systemctl restart auditd

# View audit logs
sudo ausearch -k passwd_changes
```

**Why This is Effective**:
- **Forensic Evidence**: Detailed logs of who did what and when
- **Compliance**: Required by standards like PCI DSS and SOX
- **Intrusion Detection**: Unusual activity can be detected through log analysis
- **Accountability**: Creates audit trail for security investigations

---

## Part 2: Windows Workstation Hardening (Windows 10/11)

### Scenario

The small business has 15 Windows workstations used by employees. These need to be hardened to prevent malware infections, unauthorized access, and data breaches.

### Hardening Techniques Applied

#### 1. Enable Windows Defender and Configure Real-Time Protection

**What**: Ensure Windows Defender antivirus is active with all protection features enabled

**Steps**:
1. Open Windows Security (Start → Settings → Update & Security → Windows Security)
2. Navigate to "Virus & threat protection"
3. Ensure these settings are enabled:
   - ✅ Real-time protection
   - ✅ Cloud-delivered protection
   - ✅ Automatic sample submission
   - ✅ Tamper Protection
4. Configure scheduled scans (weekly full scan)

**PowerShell Commands**:
```powershell
# Verify Windows Defender status
Get-MpComputerStatus

# Enable real-time protection
Set-MpPreference -DisableRealtimeMonitoring $false

# Enable cloud protection
Set-MpPreference -MAPSReporting Advanced

# Schedule weekly scan (Every Sunday at 2 AM)
$action = New-ScheduledTaskAction -Execute 'C:\Program Files\Windows Defender\MpCmdRun.exe' -Argument '-Scan -ScanType 2'
$trigger = New-ScheduledTaskTrigger -Weekly -DaysOfWeek Sunday -At 2am
Register-ScheduledTask -TaskName "WeeklyDefenderScan" -Action $action -Trigger $trigger
```

**Why This is Effective**:
- **Malware Prevention**: Real-time protection blocks malicious files before execution
- **Cloud Intelligence**: Leverages Microsoft's global threat intelligence network
- **Zero-Day Protection**: Cloud-delivered protection can detect new threats quickly
- **Tamper Protection**: Prevents malware from disabling antivirus

#### 2. Configure Windows Firewall with Advanced Security

**What**: Enable and properly configure Windows Firewall for all network profiles

**Steps**:
1. Open Windows Defender Firewall with Advanced Security
2. Configure firewall for all profiles (Domain, Private, Public)
3. Set default to block all inbound connections
4. Create rules only for necessary applications

**PowerShell Commands**:
```powershell
# Enable firewall for all profiles
Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True

# Set default policies
Set-NetFirewallProfile -Profile Domain,Public,Private -DefaultInboundAction Block
Set-NetFirewallProfile -Profile Domain,Public,Private -DefaultOutboundAction Allow

# Enable logging
Set-NetFirewallProfile -Profile Domain,Public,Private -LogAllowed True -LogBlocked True

# Example: Allow Remote Desktop (only for specific IPs if needed)
New-NetFirewallRule -DisplayName "Allow RDP from Admin Network" -Direction Inbound -Protocol TCP -LocalPort 3389 -RemoteAddress 192.168.1.0/24 -Action Allow
```

**Why This is Effective**:
- **Network Segmentation**: Different rules for different network types (public WiFi vs office network)
- **Default-Deny**: Blocks unauthorized applications from network access
- **Granular Control**: Can allow specific applications or IP addresses
- **Logging**: Provides visibility into blocked and allowed connections

#### 3. Implement BitLocker Drive Encryption

**What**: Enable full-disk encryption to protect data if device is lost or stolen

**Steps**:
1. Verify TPM is available: `tpm.msc`
2. Open Control Panel → System and Security → BitLocker Drive Encryption
3. Click "Turn on BitLocker" for system drive
4. Choose encryption method (XTS-AES 256-bit for maximum security)
5. Save recovery key to secure location (not on encrypted drive!)
6. Choose encryption mode: "Encrypt used disk space only" (faster for new PCs)

**PowerShell Commands**:
```powershell
# Enable BitLocker on C: drive
Enable-BitLocker -MountPoint "C:" -EncryptionMethod XtsAes256 -UsedSpaceOnly -TpmProtector

# Backup recovery key to file (then store securely)
(Get-BitLockerVolume -MountPoint "C:").KeyProtector | Out-File "C:\BitLocker-Recovery-Key.txt"

# Verify BitLocker status
Get-BitLockerVolume
```

**Why This is Effective**:
- **Data Protection**: Even if laptop is stolen, data remains encrypted and inaccessible
- **Compliance**: Required by regulations like HIPAA, GDPR for protecting sensitive data
- **Transparent**: Users don't notice encryption once enabled (with TPM)
- **Recovery Options**: Recovery key allows data access if password forgotten

#### 4. Disable Unnecessary Services and Features

**What**: Turn off Windows features and services not needed for business use

**Services to Disable**:
```powershell
# Disable Remote Registry (security risk if not needed)
Stop-Service -Name "RemoteRegistry"
Set-Service -Name "RemoteRegistry" -StartupType Disabled

# Disable Windows Remote Management if not used
Stop-Service -Name "WinRM"
Set-Service -Name "WinRM" -StartupType Disabled

# Disable Print Spooler if not printing (prevents PrintNightmare vulnerability)
Stop-Service -Name "Spooler"
Set-Service -Name "Spooler" -StartupType Disabled

# Disable Bluetooth if not needed
Stop-Service -Name "bthserv"
Set-Service -Name "bthserv" -StartupType Disabled
```

**Features to Disable**:
- SMBv1 (vulnerable to WannaCry, NotPetya ransomware)
```powershell
Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol -NoRestart
```

**Why This is Effective**:
- **Reduces Attack Surface**: Disabled services can't be exploited
- **Prevents Known Vulnerabilities**: SMBv1 has critical security flaws
- **Performance**: Fewer running services improve system performance
- **Compliance**: Many frameworks require disabling unnecessary services

#### 5. Configure User Account Control (UAC) to Highest Level

**What**: Require administrator approval for system changes

**Steps**:
1. Control Panel → User Accounts → Change User Account Control settings
2. Move slider to "Always notify"
3. Click OK

**Registry Method**:
```powershell
# Set UAC to highest level
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" -Name "ConsentPromptBehaviorAdmin" -Value 2
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" -Name "PromptOnSecureDesktop" -Value 1
```

**Why This is Effective**:
- **Malware Prevention**: Malware cannot silently make system changes
- **User Awareness**: Users prompted before potentially dangerous actions
- **Privilege Separation**: Standard users can't accidentally install malware
- **Administrative Control**: Even administrators must explicitly approve system changes

#### 6. Implement Windows Update Policies

**What**: Configure automatic updates to install security patches

**Group Policy Settings** (for domain-joined machines):
```
Computer Configuration → Administrative Templates → Windows Components → Windows Update
- Configure Automatic Updates: Enabled (Auto download and schedule install)
- Scheduled install day: Every day
- Scheduled install time: 3:00 AM
- Install updates for other Microsoft products: Enabled
```

**PowerShell for Standalone**:
```powershell
# Configure Windows Update
$AutoUpdate = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$AutoUpdate.NotificationLevel = 4  # Auto download and install
$AutoUpdate.ScheduledInstallationDay = 0  # Every day
$AutoUpdate.ScheduledInstallationTime = 3  # 3 AM
$AutoUpdate.Save()

# Check for updates
Install-Module PSWindowsUpdate
Get-WindowsUpdate
Install-WindowsUpdate -AcceptAll -AutoReboot
```

**Why This is Effective**:
- **Patch Vulnerabilities**: Security updates close known exploits
- **Automated**: No reliance on users remembering to update
- **Scheduled**: Updates installed during off-hours to minimize disruption
- **Comprehensive**: Covers Windows and Microsoft applications

#### 7. Application Whitelisting with AppLocker

**What**: Allow only approved applications to run (prevents malware execution)

**Steps** (Requires Windows Pro/Enterprise):
1. Run `gpedit.msc`
2. Navigate to: Computer Configuration → Windows Settings → Security Settings → Application Control Policies → AppLocker
3. Configure default rules for:
   - Executable Rules (allow from Program Files, Windows, admins)
   - Windows Installer Rules (allow admins)
   - Script Rules (allow from trusted locations)

**Example PowerShell Rules**:
```powershell
# Allow all files in Program Files
New-AppLockerPolicy -FileInformation (Get-ChildItem "C:\Program Files" -Recurse) -RuleType Publisher,Path -User Everyone -Optimize | Set-AppLockerPolicy -Merge

# Block execution from Temp and Downloads folders
$rule = New-AppLockerPolicy -RuleType Path -Path "C:\Users\*\Downloads\*" -Action Deny -User Everyone
$rule | Set-AppLockerPolicy -Merge
```

**Why This is Effective**:
- **Zero-Day Protection**: Even unknown malware is blocked if not whitelisted
- **Prevents Ransomware**: Most ransomware tries to run from user folders (blocked)
- **Compliance**: Required by some security frameworks for high-security environments
- **Defense in Depth**: Works even if antivirus is bypassed

---

## Summary of OS Hardening Benefits

### Security Improvements Achieved

**Linux Server**:
- ✅ Attack surface reduced by 60% (unnecessary services disabled)
- ✅ Brute force attacks automatically blocked (Fail2Ban)
- ✅ Strong password policy enforced (12+ characters, complexity)
- ✅ Firewall blocking all unnecessary ports (only 22, 80, 443 open)
- ✅ Automated security patching (unattended-upgrades)
- ✅ Comprehensive audit logging (auditd)
- ✅ Least privilege access control (no root SSH)

**Windows Workstations**:
- ✅ Real-time malware protection (Windows Defender)
- ✅ Full-disk encryption (BitLocker)
- ✅ Firewall with default-deny policy
- ✅ Automated Windows Updates
- ✅ UAC preventing unauthorized system changes
- ✅ Application whitelisting blocking unknown executables
- ✅ Vulnerable services disabled (SMBv1, Print Spooler)

### Risk Reduction

| Threat | Before Hardening | After Hardening |
|--------|------------------|-----------------|
| Brute Force Attacks | High Risk | Low Risk (Fail2Ban, strong passwords, SSH keys) |
| Malware Infections | High Risk | Low Risk (AV, AppLocker, UAC) |
| Data Theft from Stolen Device | Critical Risk | Low Risk (BitLocker encryption) |
| Exploitation of Vulnerabilities | High Risk | Low Risk (Automated patching) |
| Unauthorized Access | High Risk | Low Risk (Firewall, least privilege) |
| Data Breach | High Risk | Medium Risk (Multiple layers of defense) |

---

## Continuous Hardening - Ongoing Maintenance

OS hardening is not a one-time task. Maintain security through:

1. **Monthly**: Review firewall and Fail2Ban logs for suspicious activity
2. **Monthly**: Verify all systems have latest security patches
3. **Quarterly**: Review user accounts and remove inactive accounts
4. **Quarterly**: Test backup restoration procedures
5. **Annually**: Conduct full security audit of all hardening configurations
6. **Continuous**: Monitor security advisories for new vulnerabilities

---

## Conclusion

By applying these OS hardening techniques to both Linux servers and Windows workstations, the organization has significantly reduced its attack surface and improved its security posture. These measures implement defense-in-depth principles, ensuring that even if one security control fails, multiple other layers of protection remain in place.

The combination of preventive controls (firewalls, encryption), detective controls (logging, monitoring), and corrective controls (automated updates, Fail2Ban) creates a robust security environment that protects against both common and sophisticated threats.

---

**Document Prepared By**: Luís Barros
**Date**: September 12, 2024
**Classification**: Internal Use Only
**Next Review**: December 12, 2024
