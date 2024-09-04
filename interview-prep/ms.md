# MS

## 1. **Intro to Servers:**

* **What is a server and how does it differ from a client machine?**
  * A server is a computer or software system that provides services, data, or resources to other computers (clients) over a network. A client machine is the one that requests and uses these services.
* **Role of servers in a network environment:**
  * Servers are central to network environments, providing resources like files, applications, databases, and internet access to client machines, thus facilitating communication and resource sharing.
* **Common types of servers:**
  * **File Servers:** Store and manage files.
  * **Web Servers:** Serve web pages.
  * **Database Servers:** Host databases.
  * **Mail Servers:** Handle email services.
  * **Application Servers:** Run specific applications.

## 2. **User Authentication:**

* **What is user authentication and why is it important?**
  * User authentication is the process of verifying the identity of a user trying to access a system. It is crucial for ensuring that only authorized individuals can access sensitive information and systems.
* **Methods of user authentication:**
  * **Password-based authentication:** The most common method.
  * **Multi-factor authentication (MFA):** Uses multiple verification methods (e.g., password + OTP).
  * **Biometric authentication:** Based on unique biological traits (e.g., fingerprints).
  * **Token-based authentication:** Uses physical or digital tokens.
* **Contribution to access control:**
  * Authentication is the first step in access control, ensuring that only authenticated users are granted access to resources.

## 3. **Active Directory Structure:**

* **What is Active Directory?**
  * Active Directory (AD) is a Microsoft service used for managing and organizing network resources, such as computers, users, and services, in a Windows environment.
* **Hierarchical structure of AD:**
  * AD is organized into a hierarchy of objects: **Domains** (basic unit), **OUs (Organizational Units)** (for organizing objects within a domain), **Trees** (a collection of domains), and **Forests** (a collection of trees).
* **Organization of resources in a domain:**
  * Resources are organized into **Users**, **Groups**, **Computers**, and **Policies** within OUs and domains.
* **Need for AD:**
  * AD centralizes and automates network management, making it easier to manage large networks.
* **Difference between Workgroup and AD:**
  * **Workgroup:** A decentralized peer-to-peer network where each machine manages its own resources.
  * **AD:** A centralized directory service that provides centralized management and security.
* **Advantages of AD over Workgroup:**
  * AD provides centralized management, better scalability, and enhanced security, making it more suitable for medium to large organizations, whereas Workgroups are better suited for small, non-complex networks.

## 4. **Installing Active Directory:**

* **Process of installing AD:**
  * Install the AD Domain Services role via Server Manager, promote the server to a domain controller, and configure the domain.
* **Prerequisites:**
  * A supported version of Windows Server, DNS configuration, static IP, and sufficient hardware resources.
* **Key considerations:**
  * Plan the domain structure, OU design, and group policies according to the organization’s needs.

## 5. **Active Directory Objects:**

* **What are AD objects?**
  * AD objects represent entities like users, computers, printers, and groups within the directory.
* **Attributes of AD objects:**
  * Objects have attributes (e.g., name, email, password) that store information about the object.
* **Managing AD objects:**
  * Objects can be created, modified, or deleted using tools like Active Directory Users and Computers (ADUC) or PowerShell.

## 6. **Introduction to Group Policy:**

* **What is Group Policy?**
  * A feature in Windows that allows centralized management of settings and configurations across computers in an AD domain.
* **Role of GPOs:**
  * GPOs are the building blocks of Group Policy, containing settings applied to users and computers within a domain.
* **Examples of common settings:**
  * Password policies, software installation, and desktop environment configurations.

## 7. **Introduction to Permissions:**

* **What are permissions?**
  * Permissions control access to files, folders, and other resources, determining who can read, write, or execute them.
* **Share permissions vs. NTFS permissions:**
  * **Share Permissions:** Apply when accessing resources over a network.
  * **NTFS Permissions:** Apply both locally and over a network, offering more granular control.
* **Managing permissions:**
  * Permissions can be managed through the security tab in file/folder properties or using PowerShell.

## 8. **Authentication in General:**

* **Concept and importance:**
  * Authentication ensures that only legitimate users can access systems and data, forming the basis of network security.
* **Authentication vs. Authorization:**
  * **Authentication:** Verifies identity.
  * **Authorization:** Grants access based on identity.
* **Components of authentication:**
  * **Credential submission,** **Identity verification,** and **Access granting.**

## 9. **NTLM Authentication:**

* **What is NTLM?**
  * NTLM (NT LAN Manager) is an older Microsoft authentication protocol that uses a challenge-response mechanism.
* **Limitations of NTLM:**
  * It is vulnerable to pass-the-hash attacks and lacks mutual authentication.
* **Comparison with other methods:**
  * NTLM is less secure and flexible than Kerberos, which is preferred in modern environments.

## 10. **Kerberos Authentication:**

* **What is Kerberos?**
  * A secure authentication protocol that uses tickets to grant access to services in a network.
* **Importance and origin:**
  * Developed by MIT, Kerberos provides mutual authentication and is widely used in Windows due to its security.
* **Kerberos process:**
  * The client requests a ticket from the Key Distribution Center (KDC), which is then used to access services.
* **Advantages over other protocols:**
  * Mutual authentication, reduced risk of replay attacks, and single sign-on capabilities.

## 11. **Secure Files in AD:**

* **Securing files in AD:**
  * Use NTFS permissions, encryption, and access control lists (ACLs) to protect files within AD.
* **Best practices:**
  * Implement least privilege, regularly audit permissions, and use encryption for sensitive data.

## 12. **Remote Desktop Protocol:**

* **What is RDP?**
  * RDP is a protocol used for remote access to another computer, typically over port **3389**.
* **Security considerations:**
  * Enable Network Level Authentication (NLA), use strong passwords, and limit access through firewalls.
* **Configuring RDP:**
  * Use the Remote Desktop settings in Windows, and consider additional security tools like VPNs for remote connections.

## 13. **NetBIOS:**

* **Purpose of NetBIOS:**
  * A legacy protocol used for network communication and name resolution in early Windows networks.
* **Security risks:**
  * NetBIOS is susceptible to attacks like name spoofing and can leak information.
* **Mitigating risks:**
  * Disable NetBIOS over TCP/IP when not needed and use modern protocols like DNS.

## 14. **LLMNR:**

* **What is LLMNR?**
  * Link-Local Multicast Name Resolution (LLMNR) helps with name resolution on a local network without a DNS server.
* **Security implications:**
  * LLMNR is vulnerable to spoofing attacks, leading to potential man-in-the-middle attacks.
* **Prevention:**
  * Disable LLMNR in Group Policy and use DNS for name resolution.

## 15. **DNS:**

* **What is DNS?**
  * DNS (Domain Name System) translates human-readable domain names into IP addresses.
* **Need for DNS:**
  * Without DNS, users would need to remember IP addresses instead of domain names.
* **DNS hierarchy and servers:**
  * Organized in a hierarchy: Root, TLD (Top-Level Domain), and authoritative servers for each domain.
* **DNS security measures:**
  * Implement DNSSEC, use firewalls, and monitor for unusual activity.

## 16. **Windows Misconfigurations:**

* **Common misconfigurations:**
  * Weak passwords, unpatched systems, and improperly configured firewalls.
* **Vulnerabilities:**
  * Misconfigurations can be exploited by attackers to gain unauthorized access or spread malware.
* **Remediation:**
  * Regular audits, automated tools, and strict security policies help identify and fix misconfigurations.

## 17. **Local Administrators:**

* **What are local administrators?**
  * Users with full control over a local machine, including software installation and system changes.
* **Managing local admins:**
  * Limit the number of local admins, enforce strong passwords, and regularly review access.
* **Security implications:**
  * Too many local admins increase the risk of privilege escalation attacks.

## 18. **Brute Forcing:**

* **What is brute forcing?**
  * A method of guessing passwords or encryption keys by trying all possible combinations.
* **Common targets:**
  * Login forms, RDP, and FTP servers.
* **Defending against brute force:**
  * Implement account lockout policies, use CAPTCHAs, and deploy MFA.

## 19. **Name Poisoning:**

* **What is name poisoning?**
  * An attack where false information is inserted into a network’s name resolution process (e.g., DNS poisoning).
* **Examples:**
  * DNS spoofing and ARP poisoning.
* **Protection:**
  * Use DNSSEC, configure secure dynamic updates, and monitor network traffic for anomalies.

## 20. **General Security Awareness:**

* **Importance of security awareness:**
  * Educating users and administrators helps prevent social engineering attacks and reduces human errors.
* **Common threats:**
  * Phishing, malware, and insider threats.
* **Contributing to security:**
  * Regular training, awareness programs, and encouraging best practices help strengthen an organization’s security.
