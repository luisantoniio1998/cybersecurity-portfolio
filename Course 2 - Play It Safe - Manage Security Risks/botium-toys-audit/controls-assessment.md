# Botium Toys: Controls Assessment

## Controls Assessment Checklist

Does Botium Toys currently have this control in place?

| Control Name | Control Type | Needs to be Implemented (X) | Priority |
|--------------|--------------|------------------------------|----------|
| Least Privilege | Administrative | X | High/Critical |
| Disaster recovery plans | Administrative | X | High/Critical |
| Password policies | Administrative | X | High/Critical |
| Separation of duties | Administrative | X | High |
| Firewall | Technical | X | High/Critical |
| Intrusion Detection System (IDS) | Technical | X | High/Critical |
| Backups | Technical | X | High/Critical |
| Antivirus software | Technical | X | High/Critical |
| Manual monitoring, maintenance, and intervention for legacy systems | Technical | X | High |
| Encryption | Technical | X | High/Critical |
| Password management system | Technical | X | High |
| Locks (offices, storefront, warehouse) | Physical | X | High/Medium |
| Closed-circuit television (CCTV) surveillance | Physical | X | High/Medium |
| Fire detection/prevention (fire alarm, sprinkler system, etc.) | Physical | X | Medium |

## Detailed Controls Analysis

### Administrative Controls

#### 1. Least Privilege ✗
- **Status**: NOT IMPLEMENTED
- **Priority**: CRITICAL
- **Type**: Administrative/Preventive
- **Description**: Employees should have the minimum level of access required to perform their duties.
- **Current State**: All employees currently have unrestricted access to data, increasing risk of data breaches.
- **Recommendation**: Implement role-based access control (RBAC) immediately. Conduct access review and restrict permissions based on job functions.
- **Assets Protected**: All assets, particularly sensitive customer and financial data

#### 2. Disaster Recovery Plans ✗
- **Status**: NOT IMPLEMENTED
- **Priority**: CRITICAL
- **Type**: Administrative/Corrective
- **Description**: Procedures to restore business operations and systems after a disaster.
- **Current State**: No documented recovery procedures. Significant risk of extended downtime.
- **Recommendation**: Develop comprehensive disaster recovery plan including:
  - Recovery Time Objectives (RTO)
  - Recovery Point Objectives (RPO)
  - Backup and restoration procedures
  - Business continuity procedures
- **Assets Protected**: All systems, data, and business operations

#### 3. Password Policies ✗
- **Status**: NOT IMPLEMENTED
- **Priority**: CRITICAL
- **Type**: Administrative/Preventive
- **Description**: Standards for password complexity, length, expiration, and reuse.
- **Current State**: Weak or non-existent password standards leading to vulnerable accounts.
- **Recommendation**: Implement password policy requiring:
  - Minimum 12 characters
  - Complexity requirements (upper, lower, numbers, special characters)
  - 90-day expiration
  - No password reuse for last 12 passwords
  - Multi-factor authentication (MFA) for critical systems
- **Assets Protected**: All user accounts and systems requiring authentication

#### 4. Separation of Duties ✗
- **Status**: NOT IMPLEMENTED
- **Priority**: HIGH
- **Type**: Administrative/Preventive
- **Description**: Critical functions divided among multiple people to prevent fraud and errors.
- **Current State**: Single individuals may have complete control over critical processes.
- **Recommendation**: Implement separation of duties for:
  - Financial transactions
  - System administration
  - Security monitoring
  - Data access and modifications
- **Assets Protected**: Financial systems, sensitive data, administrative functions

### Technical Controls

#### 5. Firewall ✗
- **Status**: NOT PROPERLY IMPLEMENTED OR CONFIGURED
- **Priority**: CRITICAL
- **Type**: Technical/Preventive
- **Description**: Network security device that monitors and filters traffic based on security rules.
- **Current State**: Firewall may exist but is not properly configured, leaving network vulnerable.
- **Recommendation**:
  - Properly configure firewall rules
  - Implement default-deny policy
  - Regular firewall log review
  - Segment internal network
- **Assets Protected**: Internal network, servers, workstations, all networked assets

#### 6. Intrusion Detection System (IDS) ✗
- **Status**: NOT IMPLEMENTED
- **Priority**: CRITICAL
- **Type**: Technical/Detective
- **Description**: System that monitors network traffic for suspicious activity and alerts administrators.
- **Current State**: No ability to detect intrusions or suspicious activity in real-time.
- **Recommendation**: Deploy IDS/IPS solution with:
  - Real-time monitoring
  - Alert configuration
  - Integration with SIEM
  - Regular signature updates
- **Assets Protected**: Network infrastructure, servers, sensitive data

#### 7. Backups ✗
- **Status**: NOT IMPLEMENTED
- **Priority**: CRITICAL
- **Type**: Technical/Corrective
- **Description**: Regular copies of data stored separately to enable recovery.
- **Current State**: No backup system; risk of permanent data loss.
- **Recommendation**: Implement 3-2-1 backup strategy:
  - 3 copies of data
  - 2 different media types
  - 1 offsite backup
  - Automated daily backups
  - Monthly backup restoration testing
- **Assets Protected**: All data (customer, financial, operational)

#### 8. Antivirus Software ✗
- **Status**: NOT IMPLEMENTED
- **Priority**: CRITICAL
- **Type**: Technical/Preventive/Detective
- **Description**: Software that detects and removes malicious software.
- **Current State**: Systems vulnerable to malware and viruses.
- **Recommendation**: Deploy enterprise antivirus solution with:
  - Centralized management
  - Real-time scanning
  - Automatic updates
  - Email and web filtering
- **Assets Protected**: All endpoints (desktops, laptops, servers)

#### 9. Manual Monitoring of Legacy Systems ✗
- **Status**: NOT ADEQUATELY IMPLEMENTED
- **Priority**: HIGH
- **Type**: Technical/Detective
- **Description**: Regular monitoring and maintenance of end-of-life systems.
- **Current State**: Legacy systems not properly monitored for security issues.
- **Recommendation**:
  - Implement monitoring schedule
  - Document known vulnerabilities
  - Isolate legacy systems from main network
  - Plan for system replacement or upgrade
- **Assets Protected**: Legacy systems and data they contain

#### 10. Encryption ✗
- **Status**: NOT IMPLEMENTED
- **Priority**: CRITICAL
- **Type**: Technical/Preventive
- **Description**: Encoding data to protect confidentiality.
- **Current State**: Data transmitted and stored in plaintext; highly vulnerable to interception.
- **Recommendation**: Implement encryption for:
  - Data in transit (TLS 1.2 or higher)
  - Data at rest (AES-256)
  - Payment information (required by PCI DSS)
  - Customer personal data (required by GDPR)
- **Assets Protected**: All sensitive data, especially customer PII and payment information

#### 11. Password Management System ✗
- **Status**: NOT IMPLEMENTED
- **Priority**: HIGH
- **Type**: Technical/Preventive
- **Description**: Tool to securely store and manage passwords.
- **Current State**: Users likely storing passwords insecurely or reusing passwords.
- **Recommendation**: Deploy enterprise password manager with:
  - Encrypted password vault
  - Password generation capabilities
  - Secure sharing functionality
  - Audit logs
- **Assets Protected**: All accounts and credentials

### Physical Controls

#### 12. Locks ✗
- **Status**: INADEQUATE
- **Priority**: MEDIUM/HIGH
- **Type**: Physical/Preventive
- **Description**: Physical barriers to prevent unauthorized access.
- **Current State**: Insufficient physical access controls to offices and storage areas.
- **Recommendation**: Upgrade to:
  - Electronic badge readers for main entries
  - Key card access for sensitive areas
  - Server room with restricted access
  - After-hours security protocols
- **Assets Protected**: Physical office, equipment, inventory, servers

#### 13. CCTV Surveillance ✗
- **Status**: INADEQUATE OR NOT IMPLEMENTED
- **Priority**: MEDIUM/HIGH
- **Type**: Physical/Detective
- **Description**: Video surveillance to monitor and record physical areas.
- **Current State**: No or insufficient camera coverage.
- **Recommendation**: Install CCTV system with:
  - Coverage of all entry/exit points
  - Server room monitoring
  - 30-90 day recording retention
  - Off-site backup of recordings
- **Assets Protected**: Physical facilities, equipment, inventory

#### 14. Fire Detection/Prevention ✗
- **Status**: UNKNOWN OR INADEQUATE
- **Priority**: MEDIUM
- **Type**: Physical/Preventive/Detective
- **Description**: Systems to detect and suppress fires.
- **Current State**: Unclear if adequate fire protection exists.
- **Recommendation**: Ensure presence of:
  - Smoke detectors in all areas
  - Fire suppression system (especially in server room)
  - Fire extinguishers
  - Regular testing and maintenance
  - Emergency evacuation procedures
- **Assets Protected**: All physical assets, facilities, data center equipment

## Summary of Findings

### Critical Priority (Immediate Implementation Required):
- Least Privilege
- Disaster Recovery Plans
- Password Policies
- Firewall Configuration
- Intrusion Detection System
- Backups
- Antivirus Software
- Encryption

### High Priority (Implement Within 3-6 Months):
- Separation of Duties
- Manual Monitoring of Legacy Systems
- Password Management System
- Physical Locks/Access Control
- CCTV Surveillance

### Medium Priority (Implement Within 6-12 Months):
- Fire Detection/Prevention Enhancements

## Compliance Impact

The absence of these controls directly impacts compliance with:
- **PCI DSS**: Encryption, access controls, monitoring (required for payment processing)
- **GDPR**: Data protection, encryption, access controls (required for EU customer data)
- **SOC 2**: Access controls, monitoring, encryption (relevant for service providers)

**Total Controls Assessed**: 14
**Controls Needing Implementation**: 14 (100%)
**Critical Priority**: 8 controls
**High Priority**: 5 controls
**Medium Priority**: 1 control

---

**Assessment Date**: [Date]
**Assessed By**: IT Security Team
**Next Review**: [Date + 3 months]
