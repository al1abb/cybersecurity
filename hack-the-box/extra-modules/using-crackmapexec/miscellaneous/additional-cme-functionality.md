# Additional CME Functionality

CrackMapExec has other utilities that will be very useful in several scenarios. In this section, we will review three of them:

* Audit mode
* IPv6 support
* The completion percentage when attacking multiple devices

***

## Audit Mode

In version 5.3.0, a new mode was added: the audit mode. This mode replaces the password or hash with a character of our preference or even our favorite emoji. This feature helps to avoid blurting the screenshot when writing a customer report.

To configure the audit mode, we need to edit the configuration file located by default in `~/.cme/cme.conf` and modify the audit\_mode parameter with the character of our preference. That character will replace the password or hash when running CrackMapExec. We will use the `#` character for this example.

### **Enabling Audit Mode**

```shell-session
$ cat ~/.cme/cme.conf

[CME]
workspace = default
last_used_db = mssql
pwn3d_label = Pwn3d!
audit_mode = #

<SNIP>
```

We can now run it and see that the password is replaced in the output with `########`.

```shell-session
$ crackmapexec smb 10.129.203.121 -u robert -p Inlanefreight01!
SMB         10.129.203.121  445    MS01             [*] Windows 10.0 Build 17763 x64 (name:MS01) (domain:inlanefreight.htb) (signing:False) (SMBv1:False)
SMB         10.129.203.121  445    MS01             [+] inlanefreight.htb\robert:######## (Pwn3d!)
```

As we can see, the password in the run result is replaced by the `#` character. However, the command shows the password. For these cases, it is ideal to save the password in a file before executing the desired command.

### **Audit Mode Enabled with the Password in a File**

```shell-session
$ crackmapexec smb 10.129.203.121 -u robert -p robert_password.txt 

SMB         10.129.203.121  445    MS01             [*] Windows 10.0 Build 17763 x64 (name:MS01) (domain:inlanefreight.htb) (signing:False) (SMBv1:False)
SMB         10.129.203.121  445    MS01             [+] inlanefreight.htb\robert:######## (Pwn3d!)
```

***

## IPv6 Support

Another capability of CrackMapExec is that it supports communication over IPv6. Most organizations have IPv6 enabled by default even if they do not use it, and it is even possible that IPv6 is less monitored or understood at the log level than IPv4. This creates an opportunity for network attacks to be carried out and go undetected.

As we saw in the popular modules section, CrackMapExec allows us to identify the IPv6 of the computers with the get\_netconnections module. Let's use this module and then try to execute the command through IPv6.

### **Running get\_netconnections Module and Using IPv6**

```shell-session
$ crackmapexec smb 10.129.203.121 -u robert -p robert_password.txt -M get_netconnections

SMB         10.129.203.121  445    MS01             [*] Windows 10.0 Build 17763 x64 (name:MS01) (domain:inlanefreight.htb) (signing:False) (SMBv1:False)
SMB         10.129.203.121  445    MS01             [+] inlanefreight.htb\robert:######## (Pwn3d!)
GET_NETC... 10.129.203.121  445    MS01             [+] IP Address: ['172.16.1.5']      Search Domain: ['inlanefreight.htb', 'htb']
GET_NETC... 10.129.203.121  445    MS01             [+] IP Address: ['10.129.203.121', 'fe80::8c8a:5209:5876:537d', 'dead:beef::8c8a:5209:5876:537d', 'dead:beef::1e2'] Search Domain: ['inlanefreight.htb', 'htb']
GET_NETC... 10.129.203.121  445    MS01             [*] Saved raw output to network-connections-10.129.203.121-2022-11-16_080253.log
```

Now let's access the target over IPv6.

```shell-session
$ crackmapexec smb dead:beef::8c8a:5209:5876:537d -u robert -p robert_password.txt -x "ipconfig"

SMB         dead:beef::8c8a:5209:5876:537d 445    MS01             [*] Windows 10.0 Build 17763 x64 (name:MS01) (domain:inlanefreight.htb) (signing:False) (SMBv1:False)
SMB         dead:beef::8c8a:5209:5876:537d 445    MS01             [+] inlanefreight.htb\robert:######## (Pwn3d!)
SMB         dead:beef::8c8a:5209:5876:537d 445    MS01             [+] Executed command 
SMB         dead:beef::8c8a:5209:5876:537d 445    MS01             Windows IP Configuration
SMB         dead:beef::8c8a:5209:5876:537d 445    MS01             
SMB         dead:beef::8c8a:5209:5876:537d 445    MS01             
SMB         dead:beef::8c8a:5209:5876:537d 445    MS01             Ethernet adapter Ethernet1:
SMB         dead:beef::8c8a:5209:5876:537d 445    MS01             
SMB         dead:beef::8c8a:5209:5876:537d 445    MS01             Connection-specific DNS Suffix  . :
SMB         dead:beef::8c8a:5209:5876:537d 445    MS01             IPv4 Address. . . . . . . . . . . : 172.16.1.5
SMB         dead:beef::8c8a:5209:5876:537d 445    MS01             Subnet Mask . . . . . . . . . . . : 255.255.255.0
SMB         dead:beef::8c8a:5209:5876:537d 445    MS01             Default Gateway . . . . . . . . . :
SMB         dead:beef::8c8a:5209:5876:537d 445    MS01             
SMB         dead:beef::8c8a:5209:5876:537d 445    MS01             Ethernet adapter Ethernet0 2:
SMB         dead:beef::8c8a:5209:5876:537d 445    MS01             
SMB         dead:beef::8c8a:5209:5876:537d 445    MS01             Connection-specific DNS Suffix  . : .htb
SMB         dead:beef::8c8a:5209:5876:537d 445    MS01             IPv6 Address. . . . . . . . . . . : dead:beef::1e2
SMB         dead:beef::8c8a:5209:5876:537d 445    MS01             IPv6 Address. . . . . . . . . . . : dead:beef::8c8a:5209:5876:537d
SMB         dead:beef::8c8a:5209:5876:537d 445    MS01             Link-local IPv6 Address . . . . . : fe80::8c8a:5209:5876:537d%13
SMB         dead:beef::8c8a:5209:5876:537d 445    MS01             IPv4 Address. . . . . . . . . . . : 10.129.203.121
SMB         dead:beef::8c8a:5209:5876:537d 445    MS01             Subnet Mask . . . . . . . . . . . : 255.255.0.0
SMB         dead:beef::8c8a:5209:5876:537d 445    MS01             Default Gateway . . . . . . . . . : fe80::250:56ff:feb9:b9fc%13
SMB         dead:beef::8c8a:5209:5876:537d 445    MS01             10.129.0.1
```

***

## Completion Percent

You can now press enter while a scan is running, and CME will give you a completion percentage and the number of hosts remaining to scan. We are attacking one host at at time in the module lab, but once you find a more extensive network, you will most likely use this feature. For now, let's run the option `--shares` and hit enter before its finishes.

```shell-session
$ crackmapexec smb 10.129.203.121 -u robert -p robert_password.txt --shares

SMB         10.129.203.121  445    MS01             [*] Windows 10.0 Build 17763 x64 (name:MS01) (domain:inlanefreight.htb) (signing:False) (SMBv1:False)
SMB         10.129.203.121  445    MS01             [+] inlanefreight.htb\robert:######## (Pwn3d!)

[*] completed: 100.00% (1/1)
SMB         10.129.203.121  445    MS01             [+] Enumerated shares
SMB         10.129.203.121  445    MS01             Share           Permissions     Remark
SMB         10.129.203.121  445    MS01             -----           -----------     ------
SMB         10.129.203.121  445    MS01             ADMIN$          READ,WRITE      Remote Admin
SMB         10.129.203.121  445    MS01             C$              READ,WRITE      Default share
SMB         10.129.203.121  445    MS01             CertEnroll      READ,WRITE      Active Directory Certificate Services share
SMB         10.129.203.121  445    MS01             IPC$            READ            Remote IPC
```
