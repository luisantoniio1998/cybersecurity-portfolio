# Botium Toys: Risk Assessment

## Current Assets

Assets managed by the IT Department include:
- On-premises equipment for in-office business needs
- Employee equipment: end-user devices (desktops/laptops, smartphones), remote workstations, headsets, cables, keyboards, mice, docking stations, surveillance cameras, etc.
- Management systems and software for:
  - Accounting
  - Telecommunication
  - Database management
  - Security operations
  - Ecommerce platform
  - Inventory management
- Internet access
- Internal network
- Vendor access management
- Data center hosting services
- Data retention and storage systems
- Legacy system maintenance (end-of-life systems requiring human monitoring)

## Risk Description

Currently, there is inadequate management of assets. Additionally, Botium Toys does not have proper controls in place and may not be compliant with U.S. and international regulations and guidelines.

## Control Best Practices

The first of the five functions of the NIST CSF is **Identify**. Botium Toys will need to dedicate resources to managing assets. Additionally, they will need to determine the impact of the loss of existing assets, including systems, on business continuity.

## Risk Score

On a scale of 1 to 10, the current risk score is: **8**

This is considered a **high risk** score due to a lack of controls and adherence to necessary compliance regulations and standards.

## Additional Comments

### Critical Findings

The potential impact from the loss of an asset is rated as **medium** because the IT department does not know which assets would be lost. However, the risk of asset loss or fines from governing bodies is **high** because Botium Toys does not have all of the necessary controls in place and is not adhering to required regulations and standards related to keeping customer and vendor data private, secure, and intact.

### Specific Risk Areas

#### 1. **Asset Management**
- **Risk**: Inadequate asset inventory and tracking
- **Impact**: Inability to protect unknown or untracked assets
- **Likelihood**: High
- **Priority**: Critical

#### 2. **Access Controls**
- **Risk**: Lack of least privilege implementation
- **Impact**: Unauthorized access to sensitive data and systems
- **Likelihood**: High
- **Priority**: Critical

#### 3. **Data Protection**
- **Risk**: Insufficient encryption and data handling procedures
- **Impact**: Data breaches, loss of customer trust, regulatory fines
- **Likelihood**: High
- **Priority**: Critical

#### 4. **Compliance Gaps**
- **Risk**: Non-compliance with PCI DSS, GDPR, and other regulations
- **Impact**: Significant fines, legal consequences, loss of payment processing ability
- **Likelihood**: High (currently non-compliant)
- **Priority**: Critical

#### 5. **Physical Security**
- **Risk**: Inadequate physical controls for assets and facilities
- **Impact**: Theft, unauthorized physical access, equipment damage
- **Likelihood**: Medium
- **Priority**: High

#### 6. **Business Continuity**
- **Risk**: No documented disaster recovery or backup procedures
- **Impact**: Extended downtime, data loss, revenue impact
- **Likelihood**: Medium
- **Priority**: High

#### 7. **Legacy Systems**
- **Risk**: End-of-life systems without proper security updates
- **Impact**: Vulnerability to known exploits
- **Likelihood**: High
- **Priority**: High

#### 8. **Vendor Management**
- **Risk**: Uncontrolled vendor access to systems and data
- **Impact**: Third-party data breaches, supply chain attacks
- **Likelihood**: Medium
- **Priority**: Medium

## Administrative Controls

### Currently Lacking:
- Least Privilege
- Disaster recovery plans
- Password policies
- Access control policies
- Account management policies
- Separation of duties

### Impact of Absence:
Without these controls, employees have unrestricted access to data and systems, increasing the risk of:
- Accidental or intentional data breaches
- Insider threats
- Non-compliance with regulations
- Inability to recover from disasters
- Weak authentication mechanisms

## Technical Controls

### Currently Lacking:
- Firewall (or misconfigured)
- Intrusion Detection System (IDS)
- Encryption
- Backups
- Password management system
- Antivirus software
- Manual monitoring of legacy systems

### Impact of Absence:
Without proper technical controls:
- Network is vulnerable to attacks
- No way to detect intrusions in real-time
- Data transmitted and stored is not protected
- Cannot recover from data loss events
- Weak password security
- Malware infections possible
- Legacy systems at risk of compromise

## Physical Controls

### Currently Lacking:
- Adequate locks
- CCTV surveillance (or insufficient coverage)
- Fire detection and prevention systems
- Physical security policies for data center

### Impact of Absence:
Insufficient physical controls allow for:
- Unauthorized physical access to facilities
- Theft of equipment and data
- Physical damage to assets
- Inadequate protection of critical infrastructure

## Recommendations Summary

Based on this risk assessment, Botium Toys should immediately:

1. **Implement critical administrative controls**: least privilege, access policies, disaster recovery plans
2. **Deploy essential technical controls**: properly configured firewalls, IDS, encryption, backup systems
3. **Enhance physical security**: improve locks, surveillance, fire suppression
4. **Achieve compliance**: Meet PCI DSS and GDPR requirements
5. **Develop comprehensive documentation**: Policies, procedures, and playbooks
6. **Establish ongoing monitoring**: Regular security assessments and continuous improvement

The high risk score of 8/10 necessitates immediate action to protect the organization, its customers, and its assets.

---

**Risk Assessment Date**: [Date]
**Conducted By**: IT Security Team
**Next Review Date**: [Date + 6 months]
