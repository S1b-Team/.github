# Red Team Workflow & Methodology

> A comprehensive guide to Red Team operations following industry best practices and the MITRE ATT&CK framework.

## 🎯 Overview

This document outlines the standard workflow and methodology used by S1BGr0up for Red Team engagements. It serves as a reference for both internal operations and external contributors.

---

## 📋 Table of Contents

1. [Engagement Phases](#engagement-phases)
2. [MITRE ATT&CK Mapping](#mitre-attck-mapping)
3. [Tools & Techniques](#tools--techniques)
4. [Documentation Requirements](#documentation-requirements)
5. [OPSEC Considerations](#opsec-considerations)

---

## 🔄 Engagement Phases

### Phase 1: Planning & Reconnaissance

**Objective**: Gather intelligence about the target without direct interaction.

**Activities**:
- Define scope and rules of engagement (RoE)
- Identify target assets and infrastructure
- OSINT gathering (passive reconnaissance)
- Threat modeling and attack surface analysis

**MITRE ATT&CK Tactics**:
- Reconnaissance (TA0043)

**Key Deliverables**:
- Scope document with clear boundaries
- Asset inventory
- OSINT report
- Attack vectors identified

**Tools Category**:
```
tools/reconnaissance/
└── Passive information gathering
└── OSINT automation
└── Attack surface mapping
```

---

### Phase 2: Initial Access

**Objective**: Gain initial foothold in the target environment.

**Activities**:
- Active reconnaissance and enumeration
- Vulnerability identification
- Exploit development or adaptation
- Social engineering (if in scope)
- Credential harvesting

**MITRE ATT&CK Tactics**:
- Initial Access (TA0001)
- Execution (TA0002)

**Common Techniques**:
- Phishing (T1566)
- Exploit Public-Facing Application (T1190)
- Valid Accounts (T1078)
- External Remote Services (T1133)

**Tools Category**:
```
tools/exploitation/
└── Vulnerability scanners
└── Exploit frameworks
└── Credential tools
```

---

### Phase 3: Privilege Escalation

**Objective**: Elevate privileges to gain higher-level access.

**Activities**:
- Local enumeration
- Vulnerability scanning (internal)
- Exploit local misconfigurations
- Abuse legitimate functionality

**MITRE ATT&CK Tactics**:
- Privilege Escalation (TA0004)

**Common Techniques**:
- Exploitation for Privilege Escalation (T1068)
- Valid Accounts (T1078)
- Sudo and Sudo Caching (T1548.003)
- Setuid and Setgid (T1548.001)

**Tools Category**:
```
tools/post-exploitation/
└── Enumeration scripts
└── Privilege escalation tools
└── Kernel exploits
```

---

### Phase 4: Persistence

**Objective**: Maintain access to the compromised environment.

**Activities**:
- Install backdoors
- Create additional accounts
- Schedule tasks
- Modify system configurations

**MITRE ATT&CK Tactics**:
- Persistence (TA0003)

**Common Techniques**:
- Create Account (T1136)
- Scheduled Task/Job (T1053)
- Modify Authentication Process (T1556)
- Implant Container Image (T1525)

**Tools Category**:
```
tools/persistence/
└── Backdoor implementations
└── Persistence mechanisms
└── Rootkits (for research)
```

**⚠️ Ethics Note**: Always remove persistence mechanisms after engagement completion.

---

### Phase 5: Defense Evasion

**Objective**: Avoid detection by security controls.

**Activities**:
- Log manipulation
- Process hiding
- Traffic obfuscation
- Anti-forensics techniques

**MITRE ATT&CK Tactics**:
- Defense Evasion (TA0005)

**Common Techniques**:
- Obfuscated Files or Information (T1027)
- Indicator Removal on Host (T1070)
- Masquerading (T1036)
- Process Injection (T1055)

**Tools Category**:
```
tools/evasion/
└── Obfuscation tools
└── Log cleaners
└── Traffic tunneling
```

---

### Phase 6: Lateral Movement

**Objective**: Move through the network to reach additional targets.

**Activities**:
- Network enumeration
- Credential dumping
- Remote exploitation
- Pivot through compromised hosts

**MITRE ATT&CK Tactics**:
- Lateral Movement (TA0008)
- Discovery (TA0007)

**Common Techniques**:
- Remote Services (T1021)
- Use Alternate Authentication Material (T1550)
- Internal Spearphishing (T1534)

---

### Phase 7: Collection & Exfiltration

**Objective**: Gather and extract target data (simulation only).

**Activities**:
- Identify sensitive data
- Stage data for extraction
- Document findings
- Create proof of concept

**MITRE ATT&CK Tactics**:
- Collection (TA0009)
- Exfiltration (TA0010)

**⚠️ Critical**: In real engagements, NEVER exfiltrate actual sensitive data. Use dummy files or screenshots as proof.

---

### Phase 8: Reporting & Remediation

**Objective**: Document findings and assist with remediation.

**Activities**:
- Compile comprehensive report
- Risk assessment and prioritization
- Remediation recommendations
- Debrief with client
- Clean up (remove tools, backdoors, accounts)

**Deliverables**:
- Executive summary
- Technical report with evidence
- Remediation roadmap
- Lessons learned

---

## 🗺️ MITRE ATT&CK Mapping

All S1BGr0up tools and techniques are mapped to the MITRE ATT&CK framework for standardization.

### Framework Reference

| Tactic | ID | Tools Category |
|--------|------|----------------|
| Reconnaissance | TA0043 | `tools/reconnaissance/` |
| Initial Access | TA0001 | `tools/exploitation/` |
| Execution | TA0002 | `tools/exploitation/` |
| Persistence | TA0003 | `tools/persistence/` |
| Privilege Escalation | TA0004 | `tools/post-exploitation/` |
| Defense Evasion | TA0005 | `tools/evasion/` |
| Credential Access | TA0006 | `tools/post-exploitation/` |
| Discovery | TA0007 | `tools/reconnaissance/` |
| Lateral Movement | TA0008 | `tools/post-exploitation/` |
| Collection | TA0009 | `tools/post-exploitation/` |
| Exfiltration | TA0010 | `tools/post-exploitation/` |

**Reference**: https://attack.mitre.org/

---

## 🛠️ Tools & Techniques

### Tool Selection Criteria

1. **Reliability**: Proven effectiveness in real-world scenarios
2. **Stealth**: Minimal detection by security controls
3. **Flexibility**: Adaptable to different environments
4. **Documentation**: Well-documented usage and examples
5. **Safety**: No unintended side effects or damage

### Integration with Existing Tools

S1BGr0up tools are designed to complement, not replace, existing security tools:

- **Metasploit**: Exploit delivery and payload generation
- **Cobalt Strike**: C2 infrastructure (commercial)
- **BloodHound**: Active Directory enumeration
- **Impacket**: Python classes for network protocols
- **Nmap**: Network discovery and security auditing

---

## 📝 Documentation Requirements

### Engagement Documentation

Every Red Team engagement must document:

1. **Scope Definition**
   - In-scope assets and networks
   - Out-of-scope restrictions
   - Time windows and constraints
   - Communication protocols

2. **Rules of Engagement (RoE)**
   - Authorized activities
   - Prohibited actions
   - Escalation procedures
   - Emergency stop protocols

3. **Activity Logs**
   - Timestamp of every action
   - Commands executed
   - Tools used
   - Results obtained

4. **Evidence Collection**
   - Screenshots of critical findings
   - Proof-of-concept code
   - Network captures (sanitized)
   - Credential dumps (hashed)

5. **Clean-up Verification**
   - Tools removed
   - Accounts deleted
   - Persistence mechanisms removed
   - System state restored

---

## 🔒 OPSEC Considerations

### Operational Security Best Practices

#### 1. **Infrastructure Security**
- Use dedicated infrastructure for operations
- Implement proper network segmentation
- Encrypt all C2 communications
- Use VPNs and proxies to obfuscate source

#### 2. **Tool Security**
- No hardcoded credentials in tools
- Sanitize logs and output files
- Use secure communication channels
- Implement anti-forensics by default

#### 3. **Data Handling**
- Never store real credentials in plain text
- Encrypt sensitive engagement data
- Secure deletion of temporary files
- Follow data retention policies

#### 4. **Communication Security**
- Use encrypted channels (PGP, Signal)
- Avoid discussing sensitive details publicly
- Protect client confidentiality
- Follow NDA requirements

#### 5. **Personal OPSEC**
- Separate personal and professional identities
- Use anonymous accounts when necessary
- Protect team member identities
- Follow social media guidelines

---

## 📚 Additional Resources

### Training & Certification
- OSCP (Offensive Security Certified Professional)
- OSEP (Offensive Security Experienced Penetration Tester)
- CRTO (Certified Red Team Operator)
- GPEN (GIAC Penetration Tester)

### Reading Material
- "Red Team Field Manual" by Ben Clark
- "Operator Handbook" by Joshua Picolet
- "The Hacker Playbook" series by Peter Kim
- MITRE ATT&CK documentation

### Community
- DEFCON Red Team Village
- BSides conferences
- Local security meetups
- Online CTF platforms

---

## ⚠️ Legal & Ethical Disclaimer

**CRITICAL**: All Red Team activities must be:
- ✅ Authorized in writing before starting
- ✅ Within defined scope boundaries
- ✅ Compliant with applicable laws
- ✅ Conducted with professional ethics
- ✅ Properly documented and reported

**NEVER** conduct unauthorized security testing. Unauthorized access is illegal and unethical.

---

## 🤝 Contributing

Have suggestions for improving this methodology? See [CONTRIBUTING.md](../../CONTRIBUTING.md) for how to contribute.

---

```
[*] Professional methodology for professional operators
[*] Stay authorized. Stay ethical. Stay legal.
[*] S1BGr0up - Red Team Excellence
```

**Last Updated**: October 27, 2025
