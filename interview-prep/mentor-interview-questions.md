# Mentor Interview Questions

## Intro to Cyber

### 1) CIA Triad

The CIA Triad refers to Confidentiality, Integrity, and Availability, which are the core principles of information security. Confidentiality ensures unauthorized individuals do not access data, Integrity ensures data is accurate and unaltered, and Availability ensures that data and resources are accessible when needed.

### 2) Incident Response (High)

Incident Response is a structured approach to handling and managing security incidents or breaches. It involves detection, containment, eradication, recovery, and post-incident analysis to minimize the impact of security incidents.

### 3) SOC, Security Solutions, SIEM (Low)

* **SOC**: Security Operations Center, a facility where security analysts monitor and manage security threats and incidents.
* **Security Solutions**: Tools and strategies designed to protect information systems from threats, including firewalls, antivirus software, and encryption.
* **SIEM**: Security Information and Event Management, a system that collects, analyzes, and correlates security data across an organization to provide real-time analysis and alerting.

### 4) Digital Forensics (High)

The process of collecting, preserving, analyzing, and presenting digital evidence in a way that is legally admissible. It involves investigating digital devices and data to uncover evidence related to cybercrimes.

### 5) Digital Evidence Types, Order of Volatility, Chain of Custody

* **Digital Evidence Types**: Include files, logs, emails, and metadata.
* **Order of Volatility**: Refers to the order in which evidence should be collected based on its volatility (e.g., RAM is more volatile than hard drives).
* **Chain of Custody**: A record of who handled the evidence, ensuring its integrity and admissibility in court.

### 6) Threat Hunting (Low)

The proactive process of searching for potential threats and vulnerabilities within a network or system before they can cause harm, typically using advanced analytics and threat intelligence.

### 7) Incident Detection types, IOC/BIOC, Baseline, Behaviour Based Analysis

* **Incident Detection Types**: Can be signature-based, anomaly-based, or behavior-based.
* **IOC/BIOC**: Indicators of Compromise/Behavioral Indicators of Compromise are signs that a system has been compromised or is being targeted.
* **Baseline**: A baseline represents the normal operating conditions of a system to help identify deviations that may indicate an incident.
* **Behavior-Based Analysis**: Analyzes the behavior of systems and users to detect suspicious activities.

### 8) Threat Intelligence (Med)

Information about current and potential threats to an organization’s security. It helps in understanding the tactics, techniques, and procedures used by attackers.

### 9) Cyber Kill Chain, Case Studies (Med)

**Cyber Kill Chain**: A model describing the stages of a cyber attack, including reconnaissance, weaponization, delivery, exploitation, installation, command and control, and actions on objectives.

RSA Hack

Carbanak

### 10) Mitre Attack, Pyramid of Pain

* **Mitre ATT\&CK**: A knowledge base of adversary tactics and techniques based on real-world observations, used for threat modeling and detection.
* **Pyramid of Pain**: A model illustrating the effectiveness of different types of indicators in defending against cyber attacks, from hashes to tactics.

### 11) Passive/Active Recon (High)

* **Passive Reconnaissance**: Gathering information without directly interacting with the target, such as through public records or social media.
* **Active Reconnaissance**: Directly interacting with the target to gather information, such as through port scans or probing.

### 12) OSINT, Dark Web (Medium)

* **OSINT**: Open Source Intelligence, information collected from publicly available sources.
* **Dark Web**: A part of the internet not indexed by traditional search engines, accessible only through specific software like Tor, often associated with illegal activities.

### 13) Social Engineering

Manipulating individuals into divulging confidential information or performing actions that compromise security, such as phishing or pretexting.

### 14) Threat Actors

Individuals or groups that exploit vulnerabilities and conduct attacks against organizations or individuals, including hackers, insiders, and nation-state actors.

### 15) Malware Types (High)

Includes viruses, worms, trojans, ransomware, spyware, adware, and rootkits, each with different functionalities and methods of infection.

### 16) Attack Types (High)

Can include denial-of-service attacks, phishing, man-in-the-middle attacks, SQL injection, cross-site scripting, and more.

### 17) Risk, Risk impacts

* **Risk**: The potential for harm or loss resulting from a threat exploiting a vulnerability.
* **Risk Impacts**: The consequences of a risk event, such as financial loss, reputation damage, or operational disruption.

### 18) Security Controls

Measures implemented to protect systems and data, including preventive, detective, and corrective controls, such as firewalls, intrusion detection systems, and encryption.

### 19) Defence in Depth (Med)

A security strategy that uses multiple layers of defense to protect systems and data, ensuring that if one layer fails, others will still provide protection.

### 20) Data Loss Prevention, Data Types (High)

### 21) Encryption and Hashing (Low)

### 22) Brute Force, Rainbow Table, Dictionary Attacks (High)

### 23) Digital Signatures (Med)

### 24) GRC

## Cryptography

### 1) Symmetric/asymmetric key algorithms (Low)

### 2) Hash Functions

### 3) PKI, Diffie Hellman

### 4) Digital Signatures (Duplicate) (Low)

## Linux

### 1) Kernel (Medium)

### 2) Linux Distributions (Medium)

### 3) CLI & GUI

### 4) Linux Packet Managers

### 5) CLI Basics, Command Structures

### 6) File System Hierarchy (Med)

### 7) Wildcards (High)

### 8) Hard & Symbolic Links (Med)

### 9) Users & Groups

### 10) /etc/passwd & /etc/shadow (Med)

### 11) File Ownership and Permissions (Med)

### 12) Special Permissions, SUID & SGID bits, Sticky Bit (Med)

### 13) Linux Privileges, Sudo, /etc/sudoers (Med)

### 14) Regular Expressions, Extended Regex, Text Processing

### 15) Local & Environment Variables

### 16) Bash Scripting

### 17) General Linux Security

## Network

### 1) OSI & TCP/IP model, Differences (Med)

### 2) IP Addressing (Low)

### 3) Subnetting and CIDR Notation

### 4) Firewalls and Network Security

### 5) DNS, DNS Attacks (Low)

### 6) DHCP, DORA Process, DHCP Attacks (Low)

### 7) Switching, Switch Port Security (Low)

### 8) VLANS, VLAN Attacks (Med)

### 9) ARP Protocol, ARP Process

### 10) ARP Attacks, Mitigations

### 11) MAC, MAC Attacks, Mitigations

### 12) Routing Protocols

### 13) VPN

### 14) Application Layer Protocols, Ports

### 15) Transport Layer Protocols, TCP 3-way Handshake (high)

### 16) MTU, Encapsulation, Fragmentation

### 17) Session Layer, Protocols, SSL/TLS (ZERO)

## Microsoft

### 1) AD Structure (Med)

### 2) AD Authentication, Authorization

### 3) Kerberos in General, Kerberos Steps

### 4) Kerberos Attacks, Asreproasting, Golden Ticket, Kerberoasting, Silver Ticket (Med)

### 5) NTLM in General, NTLM Steps (Med)

### 6) NTLM Attacks, Responder

### 7) NetBIOS, LLMNR, Name Resolution Process (ZERO)

### 8) LDAP (Low)

### 9) Files/Folder Permissions in NTFS

### 10) Group Policy Objects (Med)

### 11) Workgroup and Domain differences, Domain, Local, and, Enterprise Admins

### 12) Global Catalog

### 13) AD Services

### 14) Role of DNS in Active Directory

### 15) Microsoft System Hardening

## Security Gateways

### 1) Firewall Basics (Low)

### 2) Stateless and Stateful Firewalls

### 3) Next Generation Firewalls (Low)

### 4) Unified Threat Management

### 5) Zero Trust (Med)

### 6) NAT, Dynamic NAT, PAT

### 7) Authentication Mechanisms of Fortigate (Low)

### 8) AAA (Med)

### 9) Hardening

## Python

### 1) Libraries

### 2) Web Automation

### 3) Python for Cybersecurity

### 4) Data Types

### 5) Loops
