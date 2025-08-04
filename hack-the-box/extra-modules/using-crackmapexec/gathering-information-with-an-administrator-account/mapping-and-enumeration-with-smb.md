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
