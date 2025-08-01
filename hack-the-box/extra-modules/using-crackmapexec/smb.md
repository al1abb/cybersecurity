# SMB

The SMB protocol is advantageous for recon against a Windows target. Without any authentication, we can retrieve all kinds of information, including:

|                             |                        |
| --------------------------- | ---------------------- |
| IP address                  | Target local name      |
| Windows version             | Architecture (x86/x64) |
| Fully qualified domain name | SMB signing enabled    |
| SMB version                 |                        |

## SMB Enumeration

```
crackmapexec smb 192.168.133.0/24
```

```shell-session
crackmapexec smb 192.168.133.0/24      
        
SMB         192.168.133.1   445    DESKTOP-DKCQVG2  [*] Windows 10.0 Build 19041 x64 (name:DESKTOP-DKCQVG2) (domain:DESKTOP-DKCQVG2) (signing:False) (SMBv1:False)
SMB         192.168.133.158 445    WIN-TOE6NQTR989  [*] Windows Server 2016 Datacenter 14393 x64 (name:WIN-TOE6NQTR989) (domain:inlanefreight.htb) (signing:True) (SMBv1:True)
SMB         192.168.133.157 445    WIN7             [*] Windows 7 Ultimate 7601 Service Pack 1 x64 (name:WIN7) (domain:WIN7) (signing:False) (SMBv1:True)
```

Using this simple command, we can get all of the live targets in the lab at the moment of the scan, along with the domain name, the OS version, etc.

## Getting all Hosts with SMB Signing Disabled

CrackMapExec has the option to extract all hosts where SMB signing is disabled. This option is handy when we want to use [Responder](https://github.com/lgandx/Responder) with [ntlmrelayx.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/ntlmrelayx.py) from Impacket to perform an SMBRelay attack.

We will cover relaying in this module's [Stealing Hashes](https://academy.hackthebox.com/module/84/section/809) section.

For more information about Responder and ntlmrelayx.py, we can also check out the section [Attacking SMB](https://academy.hackthebox.com/module/116/section/1167) in the [Attacking Common Services module](https://academy.hackthebox.com/module/details/116/). Additionally, this blog post: [Practical guide to NTLM Relaying in 2017](https://byt3bl33d3r.github.io/practical-guide-to-ntlm-relaying-in-2017-aka-getting-a-foothold-in-under-5-minutes.html) is worth a read through.

### **Signing Disabled - Host Enumeration**

```shell-session
crackmapexec smb 192.168.1.0/24 --gen-relay-list relaylistOutputFilename.txt
```

```shell-session
crackmapexec smb 192.168.1.0/24 --gen-relay-list relaylistOutputFilename.txt

SMB         192.168.1.101    445    DC2012A          [*] Windows Server 2012 R2 Standard 9600 x64 (name:DC2012A) (domain:OCEAN) (signing:True) (SMBv1:True)
SMB         192.168.1.102    445    DC2012B          [*] Windows Server 2012 R2 Standard 9600 x64 (name:DC2012B) (domain:EARTH) (signing:True) (SMBv1:True)
SMB         192.168.1.111    445    SERVER1          [*] Windows Server 2016 Standard Evaluation 14393 x64 (name:SERVER1) (domain:PACIFIC) (signing:False) (SMBv1:True)
SMB         192.168.1.117    445    WIN10DESK1       [*] WIN10DESK1 x64 (name:WIN10DESK1) (domain:OCEAN) (signing:False) (SMBv1:True)

<SNIP>
```

```shell-session
cat relaylistOutputFilename.txt

192.168.1.111
192.168.1.117
```

## NULL Sessions

A [NULL Session](https://en.wikipedia.org/wiki/Null_session) is an anonymous connection to an inter-process communication network service on Windows-based computers. The service is designed to allow named pipe connections but may be used by attackers to gather information about the system remotely.

When a target is vulnerable to a `NULL Session`, especially a domain controller, it will allow the attacker to gather information without having a valid domain account, such as:

* Domain users (`--users`)
* Domain groups (`--groups`)
* Password policy (`--pass-pol`)
* Share folders (`--shares`)

Let's try it on a domain controller using the following commands:

### **Enumerating the Password Policy**

```
crackmapexec smb 10.129.203.121 -u '' -p '' --pass-pol
```

```
crackmapexec smb 10.129.203.121 -u '' -p '' --pass-pol
SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] Dumping password info for domain: INLANEFREIGHT
SMB         10.129.203.121  445    DC01             Minimum password length: 7
SMB         10.129.203.121  445    DC01             Password history length: 24
SMB         10.129.203.121  445    DC01             Maximum password age: 41 days 23 hours 53 minutes 
SMB         10.129.203.121  445    DC01             
SMB         10.129.203.121  445    DC01             Password Complexity Flags: 000001
SMB         10.129.203.121  445    DC01                 Domain Refuse Password Change: 0
SMB         10.129.203.121  445    DC01                 Domain Password Store Cleartext: 0
SMB         10.129.203.121  445    DC01                 Domain Password Lockout Admins: 0
SMB         10.129.203.121  445    DC01                 Domain Password No Clear Change: 0
SMB         10.129.203.121  445    DC01                 Domain Password No Anon Change: 0
SMB         10.129.203.121  445    DC01                 Domain Password Complex: 1
SMB         10.129.203.121  445    DC01             
SMB         10.129.203.121  445    DC01             Minimum password age: 1 day 4 minutes 
SMB         10.129.203.121  445    DC01             Reset Account Lockout Counter: 30 minutes 
SMB         10.129.203.121  445    DC01             Locked Account Duration: 30 minutes 
SMB         10.129.203.121  445    DC01             Account Lockout Threshold: None
SMB         10.129.203.121  445    DC01             Forced Log off Time: Not Set
```

If we want to export this list we can use `--export [OUTPUT_FULL_FILE_PATH]`. In the following example we will use `$(pwd)` to use the current path:

#### **Exporting Password Policy**

```shell-session
crackmapexec smb 10.129.203.121 -u '' -p '' --pass-pol --export $(pwd)/passpol.txt
```

The export will be a JSON file. We can format the file using `sed` to replace single quotes with double quotes and use the `jq` application to display it.

#### **Formating exported file**

```shell-session
al1abb@htb[/htb]$ sed -i "s/'/\"/g" passpol.txt
al1abb@htb[/htb]$ cat passpol.txt | jq
{
  "min_pass_len": 1,
  "pass_hist_len": 24,
  "max_pass_age": "41 days 23 hours 53 minutes ",
  "min_pass_age": "1 day 4 minutes ",
  "pass_prop": "000000",
  "rst_accnt_lock_counter": "30 minutes ",
  "lock_accnt_dur": "30 minutes ",
  "accnt_lock_thres": "None",
  "force_logoff_time": "Not Set"
}
```

### **Enumerating Users**

```
crackmapexec smb 10.129.203.121  -u '' -p '' --users --export $(pwd)/users.txt
```

```
crackmapexec smb 10.129.203.121  -u '' -p '' --users --export $(pwd)/users.txt
SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [-] Error enumerating domain users using dc ip 10.129.203.121: NTLM needs domain\username and a password
SMB         10.129.203.121  445    DC01             [*] Trying with SAMRPC protocol
SMB         10.129.203.121  445    DC01             [+] Enumerated domain user(s)
SMB         10.129.203.121  445    DC01             inlanefreight.htb\Guest                          Built-in account for guest access to the computer/domain
SMB         10.129.203.121  445    DC01             inlanefreight.htb\carlos                         
SMB         10.129.203.121  445    DC01             inlanefreight.htb\grace                          
SMB         10.129.203.121  445    DC01             inlanefreight.htb\peter                          
SMB         10.129.203.121  445    DC01             inlanefreight.htb\alina                          Account for testing HR App. Password: HRApp123!
SMB         10.129.203.121  445    DC01             inlanefreight.htb\noemi                          
SMB         10.129.203.121  445    DC01             inlanefreight.htb\engels                         Service Account for testing
SMB         10.129.203.121  445    DC01             inlanefreight.htb\kiosko                         
SMB         10.129.203.121  445    DC01             inlanefreight.htb\testaccount                    pwd: Testing123!
SMB         10.129.203.121  445    DC01             inlanefreight.htb\mathew                         
SMB         10.129.203.121  445    DC01             inlanefreight.htb\svc_mssql
```

We can use the exported file to get a list of all users, we will later use this list

### **Extracting Users List**

```shell-session
al1abb@htb[/htb]$ sed -i "s/'/\"/g" users.txt
al1abb@htb[/htb]$ jq -r '.[]' users.txt > userslist.txt
al1abb@htb[/htb]$ cat userslist.txt
Guest
carlos
grace
peter
alina
noemi
engels
kiosko
testaccount
mathew
svc_mssql
gmsa_adm
belkis
nicole
jorge
linda
shaun
diana
patrick
elieser
```

Here we could list all domain users and the password policy without any account. This configuration is not always present, but this will help us get started on our goal of compromising the domain if it is the case.

## Enumerating Users with rid bruteforce

The `--rid-brute` option can be used to determine the users of a domain. This option is particularly useful when dealing with a domain that has NULL Authentication but has certain query restrictions. By using this option, we can enumerate the users and other objects in the domain.

```shell-session
crackmapexec smb 10.129.204.172  -u '' -p '' --rid-brute
```

```shell-session
$ crackmapexec smb 10.129.204.172  -u '' -p '' --rid-brute
SMB         10.129.204.172  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:INLANEFREIGHT.LOCAL) (signing:True) (SMBv1:False)
SMB         10.129.204.172  445    DC01             [+] Brute forcing RIDs
SMB         10.129.204.172  445    DC01             498: INLANEFREIGHT\Enterprise Read-only Domain Controllers (SidTypeGroup)
SMB         10.129.204.172  445    DC01             500: INLANEFREIGHT\Administrator (SidTypeUser)
SMB         10.129.204.172  445    DC01             501: INLANEFREIGHT\Guest (SidTypeUser)
SMB         10.129.204.172  445    DC01             502: INLANEFREIGHT\krbtgt (SidTypeUser)
SMB         10.129.204.172  445    DC01             512: INLANEFREIGHT\Domain Admins (SidTypeGroup)
SMB         10.129.204.172  445    DC01             513: INLANEFREIGHT\Domain Users (SidTypeGroup)
...SNIP...
SMB         10.129.204.172  445    DC01             1853: INLANEFREIGHT\abinateps (SidTypeUser)
SMB         10.129.204.172  445    DC01             1854: INLANEFREIGHT\bustoges (SidTypeUser)
SMB         10.129.204.172  445    DC01             1855: INLANEFREIGHT\nobseellace (SidTypeUser)
SMB         10.129.204.172  445    DC01             1856: INLANEFREIGHT\wormithe (SidTypeUser)
SMB         10.129.204.172  445    DC01             1857: INLANEFREIGHT\therbanstook (SidTypeUser)
SMB         10.129.204.172  445    DC01             1858: INLANEFREIGHT\sweend (SidTypeUser)
SMB         10.129.204.172  445    DC01             1859: INLANEFREIGHT\voge1993 (SidTypeUser)
SMB         10.129.204.172  445    DC01             1860: INLANEFREIGHT\lach1973 (SidTypeUser)
SMB         10.129.204.172  445    DC01             1861: INLANEFREIGHT\coulart77 (SidTypeUser)
SMB         10.129.204.172  445    DC01             1862: INLANEFREIGHT\whirds (SidTypeUser)
SMB         10.129.204.172  445    DC01             1863: INLANEFREIGHT\sturhe (SidTypeUser)
SMB         10.129.204.172  445    DC01             1864: INLANEFREIGHT\turittly (SidTypeUser)
```

By default, `--rid-brute` enumerate objects brute forcing RIDs up to `4000`. We can modify its behavior using `--rid-brute [MAX_RID]`.

{% hint style="info" %}
**Note:** There will be scenarios where we can brute force rids with null authentication.
{% endhint %}

## Enumerating Shares

