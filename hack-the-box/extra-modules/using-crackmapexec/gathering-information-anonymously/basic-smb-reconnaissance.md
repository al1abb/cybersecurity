# Basic SMB Reconnaissance

The SMB protocol is advantageous for recon against a Windows target. Without any authentication, we can retrieve all kinds of information, including:

|                             |                        |
| --------------------------- | ---------------------- |
| IP address                  | Target local name      |
| Windows version             | Architecture (x86/x64) |
| Fully qualified domain name | SMB signing enabled    |
| SMB version                 |                        |

***

### **SMB Enumeration**

```shell-session
$ crackmapexec smb 192.168.133.0/24      
        
SMB         192.168.133.1   445    DESKTOP-DKCQVG2  [*] Windows 10.0 Build 19041 x64 (name:DESKTOP-DKCQVG2) (domain:DESKTOP-DKCQVG2) (signing:False) (SMBv1:False)
SMB         192.168.133.158 445    WIN-TOE6NQTR989  [*] Windows Server 2016 Datacenter 14393 x64 (name:WIN-TOE6NQTR989) (domain:inlanefreight.htb) (signing:True) (SMBv1:True)
SMB         192.168.133.157 445    WIN7             [*] Windows 7 Ultimate 7601 Service Pack 1 x64 (name:WIN7) (domain:WIN7) (signing:False) (SMBv1:True)
```

Using this simple command, we can get all of the live targets in the lab at the moment of the scan, along with the domain name, the OS version, etc. As we can see in the output, the `domain parameter` of the target `192.168.133.157` is the same as the `name parameter`, meaning the target `WIN7` is not joined to the domain: `inlanefreight.htb`. Contrary to the target `WIN-TOE6NQTR989`, which is joined to the domain `inlanefreight.htb`.

We can also see one Windows 10, one Windows Server, and one Windows 7 host. Windows servers are usually rich targets full of juicy data (shares, passwords, website and database backups, etc.). All of them are 64-bit versions of Windows, which can be helpful if we need to execute a custom binary on one of them.

***

## Getting all Hosts with SMB Signing Disabled

CrackMapExec has the option to extract all hosts where SMB signing is disabled. This option is handy when we want to use [Responder](https://github.com/lgandx/Responder) with [ntlmrelayx.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/ntlmrelayx.py) from Impacket to perform an SMBRelay attack.

### **Signing Disabled - Host Enumeration**

```shell-session
$ crackmapexec smb 192.168.1.0/24 --gen-relay-list relaylistOutputFilename.txt

SMB         192.168.1.101    445    DC2012A          [*] Windows Server 2012 R2 Standard 9600 x64 (name:DC2012A) (domain:OCEAN) (signing:True) (SMBv1:True)
SMB         192.168.1.102    445    DC2012B          [*] Windows Server 2012 R2 Standard 9600 x64 (name:DC2012B) (domain:EARTH) (signing:True) (SMBv1:True)
SMB         192.168.1.111    445    SERVER1          [*] Windows Server 2016 Standard Evaluation 14393 x64 (name:SERVER1) (domain:PACIFIC) (signing:False) (SMBv1:True)
SMB         192.168.1.117    445    WIN10DESK1       [*] WIN10DESK1 x64 (name:WIN10DESK1) (domain:OCEAN) (signing:False) (SMBv1:True)

<SNIP>
```

```shell-session
$ cat relaylistOutputFilename.txt

192.168.1.111
192.168.1.117
```

We will cover relaying in this module's [Stealing Hashes](https://academy.hackthebox.com/module/84/section/809) section.

For more information about Responder and ntlmrelayx.py, we can also check out the section [Attacking SMB](https://academy.hackthebox.com/module/116/section/1167) in the [Attacking Common Services module](https://academy.hackthebox.com/module/details/116/). Additionally, this blog post: [Practical guide to NTLM Relaying in 2017](https://byt3bl33d3r.github.io/practical-guide-to-ntlm-relaying-in-2017-aka-getting-a-foothold-in-under-5-minutes.html) is worth a read through.
