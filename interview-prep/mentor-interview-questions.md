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

Active Directory (AD) is a hierarchical database system that manages users, computers, and resources.&#x20;

Its structure includes **domains** (basic units), **OUs (Organizational Units)** for organizing objects, and **trees** (hierarchical domains). Multiple trees form a **forest**, the highest level of the AD hierarchy.

### 2) AD Authentication, Authorization

In AD, **authentication** verifies a user's identity (via Kerberos or NTLM).&#x20;

**Authorization** controls what resources a user can access, using **ACLs (Access Control Lists)** that define permissions for users and groups.

### 3) Kerberos in General, Kerberos Steps

**Kerberos** is a secure network authentication protocol using tickets. Steps:

1. User requests a **Ticket Granting Ticket (TGT)** from the **KDC (Key Distribution Center)**.
2. KDC sends an encrypted TGT.
3. User uses the TGT to request access to a specific service.
4. KDC sends a service ticket, granting access.

Read [self-study.-25-08.-kerberos.md](../ms/self-study.-25-08.-kerberos.md "mention") for detailed info

### 4) Kerberos Attacks, Asreproasting, Golden Ticket, Kerberoasting, Silver Ticket (Med)

* **Asreproasting**: Attacks accounts without pre-authentication to retrieve password hashes.
* **Golden Ticket**: Allows attackers to create a valid TGT for any service by compromising the **krbtgt** account.
* **Kerberoasting**: Harvests service ticket hashes to brute-force credentials.
* **Silver Ticket**: Allows attackers to forge service tickets by compromising a service account.

### 5) NTLM in General, NTLM Steps (Med)

**NTLM** (NT LAN Manager) is an older authentication protocol. Steps:

1. User sends a request to access a resource.
2. Server challenges the user with a nonce.
3. User responds with a hashed value of the nonce and password.
4. Server verifies the hash.

### 6) NTLM Attacks, Responder

**NTLM attacks** include **pass-the-hash** (using stolen hashes to authenticate) and **relay attacks** (relaying NTLM authentication to access resources).&#x20;

**Responder** is a tool used to capture and manipulate authentication traffic (NTLM hashes) over the network.

### 7) NetBIOS, LLMNR, Name Resolution Process (ZERO)

**NetBIOS** and **LLMNR** (Link-Local Multicast Name Resolution) are protocols used for resolving names to IP addresses within a local network.&#x20;

These protocols can be exploited for attacks like **NBT-NS poisoning**, where attackers provide false responses to name resolution queries.

### 8) LDAP (Low)

**LDAP** (Lightweight Directory Access Protocol) is used to query and modify information in AD. It's a directory service protocol that operates over TCP/IP, allowing users and applications to find and retrieve information from the AD database.

### 9) Files/Folder Permissions in NTFS

In **NTFS (New Technology File System)**, permissions are assigned to files and folders to control access.&#x20;

Permissions include **read**, **write**, **execute**, and **full control**, and can be applied to users or groups via **ACLs** (Access Control Lists).

### 10) Group Policy Objects (Med)

**Group Policy Objects (GPOs)** are settings used to control user and computer environments in AD. They enforce security settings, software installation, and script execution across the network, ensuring centralized control and consistency.

### 11) Workgroup and Domain differences, Domain, Local, and, Enterprise Admins

* **Workgroup**: Decentralized model with individual machine control.
* **Domain**: Centralized control via AD, with centralized authentication and authorization.
* **Local Admins**: Control individual machines.
* **Domain Admins**: Control the domain and its resources.
* **Enterprise Admins**: Control across multiple domains in a forest.

### 12) Global Catalog

The **Global Catalog** is a distributed data repository that contains information about every object in the AD forest, used to expedite searches across domains. It stores a subset of object attributes, enabling faster lookups.

### 13) AD Services

Active Directory services include&#x20;

**AD DS (Directory Services)** for managing domains,&#x20;

**AD FS (Federation Services)** for single sign-on,&#x20;

**AD CS (Certificate Services)** for managing certificates, and&#x20;

**AD RMS (Rights Management Services)** for securing sensitive data.

### 14) Role of DNS in Active Directory

DNS is crucial in AD as it helps locate domain controllers and resolve domain names.&#x20;

AD integrates tightly with DNS, where domain names mirror DNS names, and service records (SRV) in DNS are used for domain controller discovery.

### 15) Microsoft System Hardening

System hardening involves securing Windows systems by applying best practices like disabling unnecessary services, applying patches, enforcing strong passwords, using **BitLocker** for encryption, enabling **firewalls**, and configuring **GPOs** for consistent security policies across the network.

## Security Gateways

### 1) Firewall Basics (Low)

A **firewall** is a security device that monitors and filters incoming and outgoing network traffic based on pre-established security rules. Firewalls are used to create a barrier between trusted internal networks and untrusted external networks.

### 2) Stateless and Stateful Firewalls

* **Stateless firewalls** filter traffic based on static rules, without considering the state of the connection.
* **Stateful firewalls** track active connections and make decisions based on the connection's state, offering more advanced traffic filtering.

### 3) Next Generation Firewalls (Low)

**Next-generation firewalls (NGFWs)** go beyond traditional firewalls by incorporating features like **deep packet inspection**, **intrusion prevention**, **application awareness**, and **user identity-based control**. They offer more comprehensive protection by inspecting the content of traffic, not just its headers.

### 4) Unified Threat Management

**UTM** devices combine multiple security features into a single platform, including **firewalling, antivirus, intrusion detection/prevention (IDS/IPS), VPN**, and content filtering. This simplifies network security management by consolidating protection measures.

### 5) Zero Trust (Med)

The **Zero Trust** model assumes that no one, either inside or outside the network, is inherently trusted. Access is granted based on the principle of **least privilege**, and verification is required at every access point. Key concepts include **identity verification, micro-segmentation**, and **continuous monitoring**.

### 6) NAT, Dynamic NAT, PAT

* **NAT (Network Address Translation)**: Translates private IP addresses to a public IP for routing to external networks.
* **Dynamic NAT**: Maps multiple internal IP addresses to a pool of public IP addresses on a first-come, first-served basis.
* **PAT (Port Address Translation)**: Maps multiple private IP addresses to a single public IP address, using different port numbers to distinguish traffic.

### 7) Authentication Mechanisms of Fortigate (Low)

FortiGate firewalls support multiple **authentication mechanisms**, including **local authentication**, **RADIUS**, **TACACS+**, and **LDAP**.&#x20;

Users can authenticate via methods like **two-factor authentication (2FA)**, **certificate-based authentication**, or **SSO (Single Sign-On)**.

### 8) AAA (Med)

**AAA** stands for **Authentication, Authorization, and Accounting**.

* **Authentication**: Verifies user identities (e.g., via passwords, biometrics).
* **Authorization**: Determines what resources a user can access.
* **Accounting**: Tracks user activities, logging session details for auditing purposes.

### 9) Hardening

**Hardening** refers to strengthening the security of a system by minimizing vulnerabilities. It includes:

* Disabling unnecessary services and ports.
* Regular patching.
* Implementing strong authentication policies.
* Configuring firewall rules, IDS/IPS.
* Enforcing least privilege access.

## Python

### 1) Libraries

In Python, **libraries** are collections of pre-written code that provide functionality to your programs. Common libraries include:

* **Requests** for HTTP requests.
* **Selenium** for web automation.

### 2) Web Automation

**Web automation** involves using tools to interact with web pages automatically. In Python, this can be done using libraries like **Selenium** (for browser automation), **Requests** (for making HTTP requests), and **BeautifulSoup** (for parsing HTML). It's useful for web scraping, form filling, and automated testing.

### 3) Python for Cybersecurity

Python is widely used in cybersecurity for tasks such as:

* **Network scanning** (e.g., using libraries like **Scapy**).
* **Automating security tools** (e.g., interacting with APIs for tools like **Burp Suite**).
* **Writing exploits** or payloads.
* **Forensic analysis** (e.g., analyzing log files or extracting metadata).

### 4) Data Types

Python has several built-in **data types**, including:

* **int**: Integers (e.g., 1, 100).
* **float**: Floating-point numbers (e.g., 1.5, 2.75).
* **str**: Strings (e.g., "hello").
* **list**: Ordered, mutable collections (e.g., `[1, 2, 3]`).
* **dict**: Key-value pairs (e.g., `{'key': 'value'}`).

### 5) Loops

**Loops** in Python are used for iterating over sequences or repeating tasks:

* **for loop**: Iterates over a sequence (e.g., a list or range).
* **while loop**: Repeats as long as a condition is true.
