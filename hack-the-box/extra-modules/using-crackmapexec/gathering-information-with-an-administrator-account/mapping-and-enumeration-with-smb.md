# Mapping and Enumeration with SMB

CrackMapExec comes with a lot more options when it comes to enumeration with a valid domain user account. We have covered the most used options but let's dig deeper. Here is the list of all the choices we can use once we get a valid account, even if it is not privileged:

| **Command**                                                | **Description**                                                                 |
| ---------------------------------------------------------- | ------------------------------------------------------------------------------- |
| `crackmapexec smb <target> -u <u> -p <p> --loggedon-users` | Enumerate logged users on the target                                            |
| `crackmapexec smb <target> -u <u> -p <p> --sessions`       | Enumerate active sessions on the target                                         |
| `crackmapexec smb <target> -u <u> -p <p> --disks`          | Enumerate disks on the target                                                   |
| `crackmapexec smb <target> -u <u> -p <p> --computers`      | Enumerate computer on the target domain                                         |
| `crackmapexec smb <target> -u <u> -p <p> --wmi`            | Issues the specified WMI query                                                  |
| `crackmapexec smb <target> -u <u> -p <p> --wmi-namespace`  | WMI Namespace (default: root\cimv2)                                             |
| `crackmapexec smb <target> -u <u> -p <p> --rid-brute`      | Enumerate users by bruteforcing the RID on the target                           |
| `crackmapexec smb <target> -u <u> -p <p> --local-groups`   | Enumerate local groups, if a group is specified then its members are enumerated |
| `crackmapexec smb <target> -u <u> -p <p> --shares`         | Enumerate permissions on all shares of the target                               |
| `crackmapexec smb <target> -u <u> -p <p> --users`          | Enumerate domain users on the target                                            |
| `crackmapexec smb <target> -u <u> -p <p> --groups`         | Enumerate domain groups on the target                                           |
| `crackmapexec smb <target> -u <u> -p <p> --pass-pol`       | Password policy of the domain                                                   |

Let's review the ones we have not to work before:

## Enumerate active sessions / logged users on the target

If we have compromised multiple targets, it may be worth checking active sessions, maybe there is a domain administrator, and we need to focus our effort on that particular target. To identify users in a computer, we can use the options `--sessions` and `--loggedon-users`. Sessions mean user credentials are used in the target machine even though the user is not logged on. Logged-on users are self-explanatory; it means that a user has logged on to the target machine. Bloodhound is another tool we can use to find active sessions.

### **Using sessions and loggedon-users options**

```shell-session
$ crackmapexec smb 10.129.203.121 -u robert -p Inlanefreight01! --loggedon-users
SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\robert:Inlanefreight01! 
SMB         10.129.203.121  445    DC01             [+] Enumerated loggedon users
SMB         10.129.203.121  445    DC01             INLANEFREIGHT\julio                     logon_server: DC01
SMB         10.129.203.121  445    DC01             INLANEFREIGHT\DC01$                     
SMB         10.129.203.121  445    DC01             INLANEFREIGHT\svc_workstations          logon_server: DC01
```

If we are looking for a particular user, we can use the option `--loggedon-users-filter` followed by the name of the user we are looking for. In case we are looking for multiple users, it also supports regex.

### **Using the filter option with loggedon-users**

```shell-session
$ crackmapexec smb 10.129.203.121 -u robert -p Inlanefreight01! --loggedon-users --loggedon-users-filter julio
SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\julio:Password1 (Pwn3d!)
SMB         10.129.203.121  445    DC01             [+] Enumerated loggedon users
SMB         10.129.203.121  445    DC01             INLANEFREIGHT\julio                     logon_server: DC01
```

{% hint style="info" %}
**Note:** Typically, administator permissions are required for successful execution of the `--loggedon-users` or `--sessions` switches.
{% endhint %}

## Enumerate Computers

CME can also enumerate domain computers, and it does by performing an LDAP request.

### **Enumerating Computers in the Domain**

```shell-session
$ crackmapexec smb 10.129.203.121 -u robert -p Inlanefreight01! --computers
SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\robert:Inlanefreight01! 
SMB         10.129.203.121  445    DC01             [+] Enumerated domain computer(s)                  
SMB         10.129.203.121  445    DC01             inlanefreight.htb\MS01$                         
SMB         10.129.203.121  445    DC01             inlanefreight.htb\DC01$
```

{% hint style="info" %}
**Note:** Although this option is only available in the SMB protocol, CME is doing an LDAP query.

But in netexec, it exists in both ldap and smb
{% endhint %}

## Enumerate LAPS

The `Local Administrator Password Solution (LAPS)` provides management of local account passwords of domain-joined computers. Passwords are stored in Active Directory (AD) and protected by ACL, so only eligible users can read them or request a reset. If LAPS is used inside the domain and we compromise an account that can read LAPS passwords, we can use the option `--laps` with a list of targets and execute commands or use other options such as `--sam`.

{% embed url="https://academy.hackthebox.com/storage/modules/84/cmelaps.jpg" %}

Source: [Defeating LAPS](https://wiki.porchetta.industries/smb-protocol/defeating-laps)

{% hint style="info" %}
**Note:** If the default administrator account name is not "administrator," add the username after the option `--laps username`.
{% endhint %}

***

## Enumerate Users by Brute-forcing the RID on the Target `--rid-brute`

An uncommonly used feature is the `RID Bruteforce` to build user lists. We can create a list of users with `BloodHound` or `PowerView`. However, these techniques will likely get caught and take some time to set up. By using the `--rid-brute` option of CrackMapExec, it is possible to gather a list of users by brute forcing its UserID.

### **List Local Users**

```shell-session
$ crackmapexec smb 10.129.203.121 -u grace -p Inlanefreight01! --rid-brute

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\grace:Inlanefreight01! 
SMB         10.129.203.121  445    DC01             [+] Brute forcing RIDs
SMB         10.129.203.121  445    DC01             498: INLANEFREIGHT\Enterprise Read-only Domain Controllers (SidTypeGroup)
SMB         10.129.203.121  445    DC01             500: INLANEFREIGHT\Aministrator (SidTypeUser)
SMB         10.129.203.121  445    DC01             501: INLANEFREIGHT\Guest (SidTypeUser)
SMB         10.129.203.121  445    DC01             502: INLANEFREIGHT\krbtgt (SidTypeUser)
SMB         10.129.203.121  445    DC01             512: INLANEFREIGHT\Domain Admins (SidTypeGroup)
SMB         10.129.203.121  445    DC01             513: INLANEFREIGHT\Domain Users (SidTypeGroup)
SMB         10.129.203.121  445    DC01             514: INLANEFREIGHT\Domain Guests (SidTypeGroup)
SMB         10.129.203.121  445    DC01             515: INLANEFREIGHT\Domain Computers (SidTypeGroup)
SMB         10.129.203.121  445    DC01             516: INLANEFREIGHT\Domain Controllers (SidTypeGroup)
SMB         10.129.203.121  445    DC01             517: INLANEFREIGHT\Cert Publishers (SidTypeAlias)
SMB         10.129.203.121  445    DC01             518: INLANEFREIGHT\Schema Admins (SidTypeGroup)
SMB         10.129.203.121  445    DC01             519: INLANEFREIGHT\Enterprise Admins (SidTypeGroup)
SMB         10.129.203.121  445    DC01             520: INLANEFREIGHT\Group Policy Creator Owners (SidTypeGroup)
SMB         10.129.203.121  445    DC01             521: INLANEFREIGHT\Read-only Domain Controllers (SidTypeGroup)
SMB         10.129.203.121  445    DC01             522: INLANEFREIGHT\Cloneable Domain Controllers (SidTypeGroup)
SMB         10.129.203.121  445    DC01             525: INLANEFREIGHT\Protected Users (SidTypeGroup)
SMB         10.129.203.121  445    DC01             526: INLANEFREIGHT\Key Admins (SidTypeGroup)
SMB         10.129.203.121  445    DC01             527: INLANEFREIGHT\Enterprise Key Admins (SidTypeGroup)
SMB         10.129.203.121  445    DC01             553: INLANEFREIGHT\RAS and IAS Servers (SidTypeAlias)
SMB         10.129.203.121  445    DC01             571: INLANEFREIGHT\Allowed RODC Password Replication Group (SidTypeAlias)
SMB         10.129.203.121  445    DC01             572: INLANEFREIGHT\Denied RODC Password Replication Group (SidTypeAlias)
SMB         10.129.203.121  445    DC01             1002: INLANEFREIGHT\DC01$ (SidTypeUser)
SMB         10.129.203.121  445    DC01             1103: INLANEFREIGHT\DnsAdmins (SidTypeAlias)
SMB         10.129.203.121  445    DC01             1104: INLANEFREIGHT\DnsUpdateProxy (SidTypeGroup)
SMB         10.129.203.121  445    DC01             1106: INLANEFREIGHT\julio (SidTypeUser)
SMB         10.129.203.121  445    DC01             1107: INLANEFREIGHT\david (SidTypeUser)
SMB         10.129.203.121  445    DC01             1108: INLANEFREIGHT\john (SidTypeUser)
SMB         10.129.203.121  445    DC01             1109: INLANEFREIGHT\svc_workstations (SidTypeUser)
SMB         10.129.203.121  445    DC01             2107: INLANEFREIGHT\MS01$ (SidTypeUser)
SMB         10.129.203.121  445    DC01             2606: INLANEFREIGHT\carlos (SidTypeUser)
SMB         10.129.203.121  445    DC01             2607: INLANEFREIGHT\robert (SidTypeUser)
SMB         10.129.203.121  445    DC01             2608: INLANEFREIGHT\Linux Admins (SidTypeGroup)
SMB         10.129.203.121  445    DC01             2609: INLANEFREIGHT\LINUX01$ (SidTypeUser)
```

By default, `--rid-brute` enumerate objects brute forcing RIDs up to 4000. We can modify its behavior using `--rid-brute [MAX_RID]`.

The `--rid-brute` option can be used to retrieve user names and other Active Directory objects that match the brute-forced IDs. It can also be used to enumerate domain accounts if `NULL Authentication` is enabled. It's important to remember that this option can be used in these ways.

## Enumerate Disks

An important piece that we sometimes need to remember to check is the additional disks that may exist on a server. CrackMapExec has an option `--disks` that allows us to check the disks that exist in the server.

### **Enumerating Disks**

```shell-session
$ crackmapexec smb 10.129.203.121 -u robert -p Inlanefreight01! --disks

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\robert:Inlanefreight01! 
SMB         10.129.203.121  445    DC01             [+] Enumerated disks
SMB         10.129.203.121  445    DC01             C:
SMB         10.129.203.121  445    DC01             D:
```

***

## Enumerating Local and Domain Groups

We can enumerate local groups with `--local-groups` or domain groups with `--groups`.

### **Enumerating Local Groups**

```shell-session
$ crackmapexec smb 10.129.203.121 -u robert -p Inlanefreight01! --local-groups

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\robert:Inlanefreight01! 
SMB         10.129.203.121  445    DC01             [+] Enumerated local groups
SMB         10.129.203.121  445    DC01             Cert Publishers                          membercount: 0
SMB         10.129.203.121  445    DC01             RAS and IAS Servers                      membercount: 0
SMB         10.129.203.121  445    DC01             Allowed RODC Password Replication Group  membercount: 0
SMB         10.129.203.121  445    DC01             Denied RODC Password Replication Group   membercount: 8
SMB         10.129.203.121  445    DC01             DnsAdmins                                membercount: 0
SMB         10.129.203.121  445    DC01             SQLServer2005SQLBrowserUser$DC01         membercount: 0
SMB         10.129.203.121  445    DC01             Server Operators                         membercount: 5

<SNIP>

SMB         10.129.203.121  445    DC01             Remote Management Users                  membercount: 3
SMB         10.129.203.121  445    DC01             Storage Replica Administrators           membercount: 0
```

### **Enumerating Domain Groups**

```shell-session
$ crackmapexec smb 10.129.203.121 -u robert -p Inlanefreight01! --groups                         
      
SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\robert:Inlanefreight01!          
SMB         10.129.203.121  445    DC01             [+] Enumerated domain group(s)                                                                                                            
SMB         10.129.203.121  445    DC01             LAPS_PCAdmin                             membercount: 1
SMB         10.129.203.121  445    DC01             LAPS_DCAdmin                             membercount: 0
SMB         10.129.203.121  445    DC01             SQLServer2005SQLBrowserUser$DC01         membercount: 0
SMB         10.129.203.121  445    DC01             Help Desk 2                              membercount: 0
SMB         10.129.203.121  445    DC01             Help Desk                                membercount: 0
SMB         10.129.203.121  445    DC01             Linux Admins                             membercount: 3

<SNIP>

SMB         10.129.203.121  445    DC01             Guests                                   membercount: 2
SMB         10.129.203.121  445    DC01             Users                                    membercount: 3
SMB         10.129.203.121  445    DC01             Administrators                           membercount: 5
```

If we want to get the group members, we can use `--groups [GROUP NAME]`.

### **Group Members**

```shell-session
$ crackmapexec smb 10.129.203.121 -u robert -p Inlanefreight01! --groups Administrators

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\robert:Inlanefreight01! 
SMB         10.129.203.121  445    DC01             [+] Enumerated members of domain group
SMB         10.129.203.121  445    DC01             inlanefreight.htb\htb
SMB         10.129.203.121  445    DC01             inlanefreight.htb\plaintext
SMB         10.129.203.121  445    DC01             inlanefreight.htb\Domain Admins
SMB         10.129.203.121  445    DC01             inlanefreight.htb\Enterprise Admins
SMB         10.129.203.121  445    DC01             inlanefreight.htb\Administrator
```

{% hint style="info" %}
**Note:** At the time of writing `--local-group` only works against a Domain Controller, and querying a group using the group name doesn't work.
{% endhint %}

***

## Querying WMI

[Windows Management Instrumentation (WMI)](https://learn.microsoft.com/en-us/windows/win32/wmisdk/wmi-start-page) is used for administrative operations on Windows operating systems. We can write WMI scripts or applications to automate administrative tasks on remote computers. WMI provides management data to other parts of the operating system and products, for example, System Center Operations Manager (formerly Microsoft Operations Manager (MOM)) or Windows Remote Management (WinRM).

One of the primary use of Windows Management Instrumentation (WMI) is the ability to query the WMI repository for class and instance information. For example, we can request that WMI return all the objects representing shut-down events from a remote or local system.

WMI uses TCP port 135 and a range of dynamic ports: 49152-65535 (RPC dynamic ports – Windows Vista, 2008 and above), TCP 1024-65535 (RPC dynamic ports – Windows NT4, Windows 2000, Windows 2003), or we can set up WMI to use a custom range of ports.

Let's use, for example, WMI to query if a remote computer has the [Sysmon](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon) application running and to display the Caption and ProcessId, the WMI query we will use is `SELECT Caption,ProcessId FROM Win32_Process WHERE Caption LIKE '%sysmon%'`:

### **Using WMI to Query if Sysmon is Running**

```shell-session
$ crackmapexec smb 10.129.203.121 -u robert -p Inlanefreight01! --wmi "SELECT Caption,ProcessId FROM Win32_Process WHERE Caption LIKE '%sysmon%'"

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\robert:Inlanefreight01! 
SMB         10.129.203.121  445    DC01             Caption => Sysmon64.exe
SMB         10.129.203.121  445    DC01             ProcessId => 3220
```

WMI organizes its classes in a hierarchical namespace. To perform a query, we must know the Class Name and the Namespace in which it is located. In the above example, query the class `Win32_Process` in the namespace `root\cimv2`. We didn't specify the namespace because, by default, CME use `root\cimv2` (we can see that information in the --help menu).

To query another namespace, we need to specify it. Let's, for example, query `MSPower_DeviceEnable` class, which is within the namespace `root\WMI`. This class holds information about devices that should dynamically power on and off while the system works. To learn more about how to find WMI classes that are related to a specific topic, we can use [Microsoft](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_wmi?view=powershell-5.1#finding-wmi-classes) and 3rd party documentation from [wutils.com](https://wutils.com/wmi/).

### **Quering root\WMI Namespace**

```shell-session
$ crackmapexec smb 10.129.203.121 -u robert -p Inlanefreight01! --wmi "SELECT * FROM MSPower_DeviceEnable" --wmi-namespace "root\WMI" 

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\robert:Inlanefreight01! 
SMB         10.129.203.121  445    DC01             InstanceName => PCI\VEN_15AD&DEV_0779&SUBSYS_077915AD&REV_00\4&23f707fc&0&00B8_0
SMB         10.129.203.121  445    DC01             Active => True
SMB         10.129.203.121  445    DC01             Enable => True
SMB         10.129.203.121  445    DC01             
SMB         10.129.203.121  445    DC01             InstanceName => PCI\VEN_8086&DEV_10D3&SUBSYS_07D015AD&REV_00\005056FFFFB9E8F200_0
SMB         10.129.203.121  445    DC01             Active => True
SMB         10.129.203.121  445    DC01             Enable => True
SMB         10.129.203.121  445    DC01             
SMB         10.129.203.121  445    DC01             InstanceName => USB\ROOT_HUB30\5&da8887e&0&0_0
SMB         10.129.203.121  445    DC01             Active => True
SMB         10.129.203.121  445    DC01             Enable => True
SMB         10.129.203.121  445    DC01             
SMB         10.129.203.121  445    DC01             InstanceName => USB\VID_0E0F&PID_0003&MI_00\7&2a0405e8&0&0000_0
SMB         10.129.203.121  445    DC01             Active => True
SMB         10.129.203.121  445    DC01             Enable => True
SMB         10.129.203.121  445    DC01             
SMB         10.129.203.121  445    DC01             InstanceName => USB\VID_0E0F&PID_0003&MI_01\7&2a0405e8&0&0001_0
SMB         10.129.203.121  445    DC01             Active => True
SMB         10.129.203.121  445    DC01             Enable => True
```

{% hint style="info" %}
**Note:** Commonly, to query WMI, we will need to have administrative privileges, but an administrator can configure a non-administrator account to query WMI. If that's the case, we can use a non-administrator account to perform WMI queries.
{% endhint %}

To learn more about WMI Query Language (WQL), we can read [Microsoft's Documentation](https://learn.microsoft.com/en-us/windows/win32/wmisdk/wql-sql-for-wmi).
