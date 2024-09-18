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

* **Data Loss Prevention (DLP)**: Technologies and practices to prevent unauthorized access to, or leakage of, sensitive data.
* **Data Types**: Can include personal data, financial data, intellectual property, and more.

### 21) Encryption and Hashing (Low)

* **Encryption**: The process of converting data into a coded format to prevent unauthorized access.
* **Hashing**: The process of generating a fixed-size hash value from data, used to ensure data integrity and for secure storage of passwords.

### 22) Brute Force, Rainbow Table, Dictionary Attacks (High)

* **Brute Force**: Trying all possible combinations to crack passwords or encryption.
* **Rainbow Table**: Precomputed tables for reversing cryptographic hash functions to crack passwords faster.
* **Dictionary Attacks**: Using a predefined list of potential passwords to crack hashed passwords.

### 23) Digital Signatures (Med)

A cryptographic technique used to verify the authenticity and integrity of digital messages or documents. It involves a private key for signing and a public key for verification.

### 24) GRC

Governance, Risk, and Compliance; a framework for managing an organization’s overall governance, risk management, and compliance with regulations.

## Cryptography

### 1) Symmetric/asymmetric key algorithms (Low)

* **Symmetric Key**: Uses the same key for encryption and decryption (e.g., AES).
* **Asymmetric Key**: Uses a pair of keys, one for encryption and one for decryption (e.g., RSA).

### 2) Hash Functions

Algorithms that produce a fixed-size hash value from input data, used for data integrity checks and password storage (e.g., SHA-256).

### 3) PKI, Diffie Hellman

* **PKI (Public Key Infrastructure)**: A framework for managing digital certificates and public-key encryption.
* **Diffie-Hellman**: A method for securely exchanging cryptographic keys over a public channel.

### 4) Digital Signatures (Duplicate) (Low)

A cryptographic technique used to verify the authenticity and integrity of digital messages or documents. It involves a private key for signing and a public key for verification.

## Linux

### 1) Kernel (Medium)

The core component of the Linux operating system that manages system resources and hardware communication.

### 2) Linux Distributions (Medium)

Ubuntu, Debian, RedHat, Kali Linux, Fedora, CentOS

### 3) CLI & GUI

* **CLI (Command Line Interface)**: A text-based interface for interacting with the operating system.
* **GUI (Graphical User Interface)**: A visual interface with windows, icons, and menus for user interaction.

### 4) Linux Packet Managers

Tools for managing software packages, including installation, updates, and removal (e.g., APT for Debian-based systems, YUM for Red Hat-based systems).

### 5) CLI Basics, Command Structures

Command structure generally follows `command [options] [arguments]`.

### 6) File System Hierarchy (Med)

The directory structure of Linux systems, including directories like `/home`, `/etc`, and `/var`.

All directories and their purposes:

### 7) Wildcards (High)

Symbols used in commands to represent multiple files or directories (e.g., `*` for any characters, `?` for a single character).

### 8) Hard & Symbolic Links (Med)

* **Hard Links**: Multiple directory entries for a single file.
* **Symbolic Links**: References to another file or directory, similar to shortcuts.

### 9) Users & Groups

Users have individual accounts, while groups are collections of users for managing permissions.

### 10) /etc/passwd & /etc/shadow (Med)

* **/etc/passwd**: Contains user account information.
* **/etc/shadow**: Contains password information and account expiration data.

### 11) File Ownership and Permissions (Med)

Files and directories are owned by users and groups with specific permissions (read, write, execute) assigned to the owner, group, and others.

### 12) Special Permissions, SUID & SGID bits, Sticky Bit (Med)

* **SUID (Set User ID)**: Allows a file to be executed with the permissions of the file owner.
* **SGID (Set Group ID)**: Allows a file to be executed with the permissions of the group owner or sets the group ID on new files.
* **Sticky Bit**: Restricts file deletion to the file owner, root, or directory owner.

### 13) Linux Privileges, Sudo, /etc/sudoers (Med)

Linux privileges define what a user or process can access. The **root user** has full control, while **regular users** have restricted permissions.

**Sudo** allows users to execute commands with elevated privileges without switching to the root account.&#x20;

The **/etc/sudoers** file manages who can use `sudo` and for which commands, edited via `visudo` to avoid syntax errors.

### 14) Regular Expressions, Extended Regex, Text Processing

Regular expressions (regex) are patterns used for string matching in text.

* **Basic regex**: Supports simple patterns (`^`, `$`, `.`).
* **Extended regex (ERE)**: Adds more features like `+`, `?`, and `|` for advanced matching.\
  Text processing tools like **grep**, **sed**, and **awk** allow searching, editing, and manipulating text based on regex patterns.

### 15) Local & Environment Variables

**Local variables** are temporary and limited to the current shell session, while **environment variables** are system-wide and affect all processes.&#x20;

They store information like the current user (`$USER`), home directory (`$HOME`), and paths (`$PATH`). Use `export` to make a variable an environment variable.

### 16) Bash Scripting

Bash scripting automates tasks by running a series of shell commands in a script. It uses control structures like loops (`for`, `while`), conditionals (`if`, `else`), and variables. It's widely used for system administration and automation tasks.

### 17) General Linux Security

Linux security involves:

* **File permissions**: Control access to files (read, write, execute).
* **Updates**: Regular patching to fix vulnerabilities.\
  Always follow the principle of **least privilege** to minimize security risks.

## Network

### 1) OSI & TCP/IP model, Differences (Med)

The **OSI model** has 7 layers (Physical, Data Link, Network, Transport, Session, Presentation, Application), while the **TCP/IP model** has 4 layers (Network Interface, Internet, Transport, Application).&#x20;

OSI is **theoretical**, while TCP/IP is **practical** and used in real-world networking. TCP/IP combines multiple OSI layers, e.g., OSI's Application, Presentation, and Session layers are all part of the TCP/IP Application layer.

### 2) IP Addressing (Low)

An IP address is a unique identifier for a device on a network. There are two types: **IPv4** (32-bit, e.g., 192.168.1.1) and **IPv6** (128-bit, e.g., 2001:db8::1). They allow devices to send/receive data across networks.

### 3) Subnetting and CIDR Notation

**Subnetting** divides a network into smaller subnets to optimize traffic.&#x20;

**CIDR** (Classless Inter-Domain Routing) represents IP addresses with a prefix to indicate the network part, e.g., 192.168.1.0/24, where `/24` shows the number of bits in the network portion.

### 4) Firewalls and Network Security

**Firewalls** control incoming and outgoing traffic based on security rules.&#x20;

They can be **stateless** (simple packet filtering) or **stateful** (track connection states). They form the first line of defense against external threats by blocking unauthorized access.

### 5) DNS, DNS Attacks (Low)

**DNS** translates domain names (e.g., google.com) into IP addresses.&#x20;

**DNS attacks** include **DNS spoofing** (providing fake IPs to redirect traffic) and **DNS amplification** (used in DDoS attacks to flood targets with traffic).

### 6) DHCP, DORA Process, DHCP Attacks (Low)

**DHCP** assigns IP addresses automatically to devices.&#x20;

The **DORA process** (Discover, Offer, Request, Acknowledge) ensures proper address assignment.&#x20;

**DHCP attacks** include **DHCP starvation** (consuming all available IPs) and **DHCP spoofing** (setting up a rogue DHCP server).

### 7) Switching, Switch Port Security (Low)

**Switches** forward traffic within a network based on MAC addresses.&#x20;

**Switch port security** involves limiting the devices that can connect to a switch port (e.g., via MAC address filtering) to prevent unauthorized access.

### 8) VLANS, VLAN Attacks (Med)

**VLANs** (Virtual LANs) logically segment networks within a switch, improving security and traffic management.&#x20;

**VLAN attacks** include **VLAN hopping**, where an attacker sends tagged packets to access restricted VLANs. Mitigation involves proper VLAN configuration and preventing unauthorized tagging.

### 9) ARP Protocol, ARP Process

**ARP** (Address Resolution Protocol) translates IP addresses into MAC addresses for communication within the same network. The process involves a device sending an ARP request to find the MAC address associated with a specific IP.

### 10) ARP Attacks, Mitigations

**ARP attacks** include **ARP spoofing**, where attackers send false ARP responses to redirect traffic. **Mitigations** include static ARP entries, enabling **Dynamic ARP Inspection (DAI)**, and using encrypted communication (e.g., VPNs).

### 11) MAC, MAC Attacks, Mitigations

A **MAC address** is a unique hardware identifier for network interfaces. **MAC attacks**, such as **MAC flooding**, overwhelm a switch's MAC table, turning it into a hub. Mitigations include limiting the number of MAC addresses per port and enabling **port security**.

### 12) Routing Protocols

**Routing protocols** determine how data is routed between networks.&#x20;

Examples include **RIP** (Routing Information Protocol), **OSPF** (Open Shortest Path First), and **BGP** (Border Gateway Protocol). These protocols ensure the best path is chosen for data transmission.

### 13) VPN

A **VPN** (Virtual Private Network) creates a secure, encrypted connection over a public network. It ensures privacy and data integrity by masking a user’s IP address and encrypting transmitted data, commonly used for secure remote access.

### 14) Application Layer Protocols, Ports

Common **Application Layer protocols** include **HTTP/HTTPS (80/443)**, **FTP (21)**, **SMTP (25)**, and **DNS (53)**.&#x20;

These protocols enable communication between applications across the network, handling high-level functions like web browsing and file transfers.

### 15) Transport Layer Protocols, TCP 3-way Handshake (high)

The **Transport Layer** protocols include **TCP** (Transmission Control Protocol) and **UDP** (User Datagram Protocol).&#x20;

**TCP** is reliable and connection-oriented, using a **3-way handshake** (SYN, SYN-ACK, ACK) to establish a connection, ensuring data is reliably transmitted.

### 16) MTU, Encapsulation, Fragmentation

**MTU** (Maximum Transmission Unit) defines the largest packet size that can be transmitted over a network.&#x20;

**Encapsulation** wraps data in protocol headers as it moves through the OSI layers.&#x20;

**Fragmentation** breaks large packets into smaller ones to fit the MTU size of the network.

### 17) Session Layer, Protocols, SSL/TLS (ZERO)

The **Session Layer** establishes, maintains, and terminates communication sessions.&#x20;

Protocols include **NetBIOS** and **PPTP**.&#x20;

**SSL** (Secure Sockets Layer) and **TLS** (Transport Layer Security) are cryptographic protocols that secure data during communication sessions over the internet.

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
