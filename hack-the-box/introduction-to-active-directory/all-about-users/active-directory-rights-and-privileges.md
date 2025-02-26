# Active Directory Rights and Privileges

Rights and privileges are the cornerstones of AD management and, if mismanaged, can easily lead to abuse by attackers or penetration testers. Access rights and privileges are two important topics in AD (and infosec in general), and we must understand the difference. `Rights` are typically assigned to users or groups and deal with permissions to `access` an object such as a file, while `privileges` grant a user permission to `perform an action` such as run a program, shut down a system, reset passwords, etc. Privileges can be assigned individually to users or conferred upon them via built-in or custom group membership. Windows computers have a concept called `User Rights Assignment`, which, while referred to as rights, are actually types of privileges granted to a user. We will discuss these later in this section. We must have a firm grasp of the differences between rights and privileges in a broader sense and precisely how they apply to an AD environment.

## Built-in AD Groups

AD contains many [default or built-in security groups](https://docs.microsoft.com/en-us/windows/security/identity-protection/access-control/active-directory-security-groups), some of which grant their members powerful rights and privileges which can be abused to escalate privileges within a domain and ultimately gain Domain Admin or SYSTEM privileges on a Domain Controller (DC). Membership in many of these groups should be tightly managed as excessive group membership/privileges is a common flaw in many AD networks that attackers look to abuse. Some of the most common built-in groups are listed below.

<table><thead><tr><th width="321">Group Name</th><th>Description</th></tr></thead><tbody><tr><td><code>Account Operators</code></td><td>Members can create and modify most types of accounts, including those of users, local groups, and global groups, and members can log in locally to domain controllers. They cannot manage the Administrator account, administrative user accounts, or members of the Administrators, Server Operators, Account Operators, Backup Operators, or Print Operators groups.</td></tr><tr><td><code>Administrators</code></td><td>Members have full and unrestricted access to a computer or an entire domain if they are in this group on a Domain Controller.</td></tr><tr><td><code>Backup Operators</code></td><td>Members can back up and restore all files on a computer, regardless of the permissions set on the files. Backup Operators can also log on to and shut down the computer. Members can log onto DCs locally and should be considered Domain Admins. They can make shadow copies of the SAM/NTDS database, which, if taken, can be used to extract credentials and other juicy info.</td></tr><tr><td><code>DnsAdmins</code></td><td>Members have access to network DNS information. The group will only be created if the DNS server role is or was at one time installed on a domain controller in the domain.</td></tr><tr><td><code>Domain Admins</code></td><td>Members have full access to administer the domain and are members of the local administrator's group on all domain-joined machines.</td></tr><tr><td><code>Domain Computers</code></td><td>Any computers created in the domain (aside from domain controllers) are added to this group.</td></tr><tr><td><code>Domain Controllers</code></td><td>Contains all DCs within a domain. New DCs are added to this group automatically.</td></tr><tr><td><code>Domain Guests</code></td><td>This group includes the domain's built-in Guest account. Members of this group have a domain profile created when signing onto a domain-joined computer as a local guest.</td></tr><tr><td><code>Domain Users</code></td><td>This group contains all user accounts in a domain. A new user account created in the domain is automatically added to this group.</td></tr><tr><td><code>Enterprise Admins</code></td><td>Membership in this group provides complete configuration access within the domain. The group only exists in the root domain of an AD forest. Members in this group are granted the ability to make forest-wide changes such as adding a child domain or creating a trust. The Administrator account for the forest root domain is the only member of this group by default.</td></tr><tr><td><code>Event Log Readers</code></td><td>Members can read event logs on local computers. The group is only created when a host is promoted to a domain controller.</td></tr><tr><td><code>Group Policy Creator Owners</code></td><td>Members create, edit, or delete Group Policy Objects in the domain.</td></tr><tr><td><code>Hyper-V Administrators</code></td><td>Members have complete and unrestricted access to all the features in Hyper-V. If there are virtual DCs in the domain, any virtualization admins, such as members of Hyper-V Administrators, should be considered Domain Admins.</td></tr><tr><td><code>IIS_IUSRS</code></td><td>This is a built-in group used by Internet Information Services (IIS), beginning with IIS 7.0.</td></tr><tr><td><code>Pre–Windows 2000 Compatible Access</code></td><td>This group exists for backward compatibility for computers running Windows NT 4.0 and earlier. Membership in this group is often a leftover legacy configuration. It can lead to flaws where anyone on the network can read information from AD without requiring a valid AD username and password.</td></tr><tr><td><code>Print Operators</code></td><td>Members can manage, create, share, and delete printers that are connected to domain controllers in the domain along with any printer objects in AD. Members are allowed to log on to DCs locally and may be used to load a malicious printer driver and escalate privileges within the domain.</td></tr><tr><td><code>Protected Users</code></td><td>Members of this <a href="https://docs.microsoft.com/en-us/windows/security/identity-protection/access-control/active-directory-security-groups#protected-users">group</a> are provided additional protections against credential theft and tactics such as Kerberos abuse.</td></tr><tr><td><code>Read-only Domain Controllers</code></td><td>Contains all Read-only domain controllers in the domain.</td></tr><tr><td><code>Remote Desktop Users</code></td><td>This group is used to grant users and groups permission to connect to a host via Remote Desktop (RDP). This group cannot be renamed, deleted, or moved.</td></tr><tr><td><code>Remote Management Users</code></td><td>This group can be used to grant users remote access to computers via <a href="https://docs.microsoft.com/en-us/windows/win32/winrm/portal">Windows Remote Management (WinRM)</a></td></tr><tr><td><code>Schema Admins</code></td><td>Members can modify the Active Directory schema, which is the way all objects with AD are defined. This group only exists in the root domain of an AD forest. The Administrator account for the forest root domain is the only member of this group by default.</td></tr><tr><td><code>Server Operators</code></td><td>This group only exists on domain controllers. Members can modify services, access SMB shares, and backup files on domain controllers. By default, this group has no members.</td></tr></tbody></table>

Below we have provided some output regarding domain admins and server operators.

### **Server Operators Group Details**

```powershell
PS C:\htb>  Get-ADGroup -Identity "Server Operators" -Properties *

adminCount                      : 1
CanonicalName                   : INLANEFREIGHT.LOCAL/Builtin/Server Operators
CN                              : Server Operators
Created                         : 10/27/2021 8:14:34 AM
createTimeStamp                 : 10/27/2021 8:14:34 AM
Deleted                         : 
Description                     : Members can administer domain servers
DisplayName                     : 
DistinguishedName               : CN=Server Operators,CN=Builtin,DC=INLANEFREIGHT,DC=LOCAL
dSCorePropagationData           : {10/28/2021 1:47:52 PM, 10/28/2021 1:44:12 PM, 10/28/2021 1:44:11 PM, 10/27/2021 
                                  8:50:25 AM...}
GroupCategory                   : Security
GroupScope                      : DomainLocal
groupType                       : -2147483643
HomePage                        : 
instanceType                    : 4
isCriticalSystemObject          : True
isDeleted                       : 
LastKnownParent                 : 
ManagedBy                       : 
MemberOf                        : {}
Members                         : {}
Modified                        : 10/28/2021 1:47:52 PM
modifyTimeStamp                 : 10/28/2021 1:47:52 PM
Name                            : Server Operators
nTSecurityDescriptor            : System.DirectoryServices.ActiveDirectorySecurity
ObjectCategory                  : CN=Group,CN=Schema,CN=Configuration,DC=INLANEFREIGHT,DC=LOCAL
ObjectClass                     : group
ObjectGUID                      : 0887487b-7b07-4d85-82aa-40d25526ec17
objectSid                       : S-1-5-32-549
ProtectedFromAccidentalDeletion : False
SamAccountName                  : Server Operators
sAMAccountType                  : 536870912
sDRightsEffective               : 0
SID                             : S-1-5-32-549
SIDHistory                      : {}
systemFlags                     : -1946157056
uSNChanged                      : 228556
uSNCreated                      : 12360
whenChanged                     : 10/28/2021 1:47:52 PM
whenCreated                     : 10/27/2021 8:14:34 AM
```

As we can see above, the default state of the `Server Operators` group is to have no members and is a domain local group by default.

### **Domain Admins Group Membership**

In contrast, the `Domain Admins` group seen below has several members and service accounts assigned to it. Domain Admins are also Global groups instead of domain local. More on group membership can be found later in this module. Be wary of who, if anyone, you give access to these groups. An attacker could easily gain the keys to the enterprise if they gain access to a user assigned to these groups.

```powershell
PS C:\htb>  Get-ADGroup -Identity "Domain Admins" -Properties * | select DistinguishedName,GroupCategory,GroupScope,Name,Members

DistinguishedName : CN=Domain Admins,CN=Users,DC=INLANEFREIGHT,DC=LOCAL
GroupCategory     : Security
GroupScope        : Global
Name              : Domain Admins
Members           : {CN=htb-student_adm,CN=Users,DC=INLANEFREIGHT,DC=LOCAL, CN=sharepoint
                    admin,CN=Users,DC=INLANEFREIGHT,DC=LOCAL, CN=FREIGHTLOGISTICSUSER,OU=Service
                    Accounts,OU=Corp,DC=INLANEFREIGHT,DC=LOCAL, CN=PROXYAGENT,OU=Service
                    Accounts,OU=Corp,DC=INLANEFREIGHT,DC=LOCAL...}
```

## User Rights Assignment

Depending on their current group membership, and other factors such as privileges that administrators can assign via Group Policy (GPO), users can have various rights assigned to their account. This Microsoft article on [User Rights Assignment](https://docs.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/user-rights-assignment) provides a detailed explanation of each of the user rights that can be set in Windows. Not every right listed here is important to us from a security standpoint as penetration testers or defenders, but some rights granted to an account can lead to unintended consequences such as privilege escalation or access to sensitive files. For example, let's say we can gain write access over a Group Policy Object (GPO) applied to an OU containing one or more users that we control. In this example, we could potentially leverage a tool such as [SharpGPOAbuse](https://github.com/FSecureLABS/SharpGPOAbuse) to assign targeted rights to a user. We may perform many actions in the domain to further our access with these new rights. A few examples include:

<table data-header-hidden><thead><tr><th width="336"></th><th></th></tr></thead><tbody><tr><td><strong>Privilege</strong></td><td><strong>Description</strong></td></tr><tr><td><code>SeRemoteInteractiveLogonRight</code></td><td>This privilege could give our target user the right to log onto a host via Remote Desktop (RDP), which could potentially be used to obtain sensitive data or escalate privileges.</td></tr><tr><td><code>SeBackupPrivilege</code></td><td>This grants a user the ability to create system backups and could be used to obtain copies of sensitive system files that can be used to retrieve passwords such as the SAM and SYSTEM Registry hives and the NTDS.dit Active Directory database file.</td></tr><tr><td><code>SeDebugPrivilege</code></td><td>This allows a user to debug and adjust the memory of a process. With this privilege, attackers could utilize a tool such as <a href="https://github.com/ParrotSec/mimikatz">Mimikatz</a> to read the memory space of the Local System Authority (LSASS) process and obtain any credentials stored in memory.</td></tr><tr><td><code>SeImpersonatePrivilege</code></td><td>This privilege allows us to impersonate a token of a privileged account such as <code>NT AUTHORITY\SYSTEM</code>. This could be leveraged with a tool such as JuicyPotato, RogueWinRM, PrintSpoofer, etc., to escalate privileges on a target system.</td></tr><tr><td><code>SeLoadDriverPrivilege</code></td><td>A user with this privilege can load and unload device drivers that could potentially be used to escalate privileges or compromise a system.</td></tr><tr><td><code>SeTakeOwnershipPrivilege</code></td><td>This allows a process to take ownership of an object. At its most basic level, we could use this privilege to gain access to a file share or a file on a share that was otherwise not accessible to us.</td></tr></tbody></table>

There are many techniques available to abuse user rights detailed [here](https://blog.palantir.com/windows-privilege-abuse-auditing-detection-and-defense-3078a403d74e) and [here](https://book.hacktricks.wiki/en/windows-hardening/windows-local-privilege-escalation/privilege-escalation-abusing-tokens.html). Though outside the scope of this module, it is essential to understand the impact that assigning the wrong privilege to an account can have within Active Directory. A small admin mistake can lead to a complete system or enterprise compromise.

