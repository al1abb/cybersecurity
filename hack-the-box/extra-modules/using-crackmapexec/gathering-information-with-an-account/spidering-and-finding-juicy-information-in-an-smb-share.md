# Spidering and Finding Juicy Information in an SMB Share

Companies need to store information in a centralized location to enable collaboration between people and departments. Usually, one department handles information that another department has to process. One of the most common ways companies allow this form of collaboration is through shared folders.

We can use the option `--shares` to request access to the share folders using the accounts we have gained access to and identify if they have access.

### **Identifying if Accounts Have Access to Shared Folders**

```shell-session
$ crackmapexec smb 10.129.203.121 -u grace -p Inlanefreight01! --shares

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\grace:Inlanefreight01! 
SMB         10.129.203.121  445    DC01             [+] Enumerated shares
SMB         10.129.203.121  445    DC01             Share           Permissions     Remark
SMB         10.129.203.121  445    DC01             -----           -----------     ------
SMB         10.129.203.121  445    DC01             ADMIN$                          Remote Admin
SMB         10.129.203.121  445    DC01             C$                              Default share
SMB         10.129.203.121  445    DC01             carlos                          
SMB         10.129.203.121  445    DC01             CertEnroll      READ            Active Directory Certificate Services share
SMB         10.129.203.121  445    DC01             D$                              Default share
SMB         10.129.203.121  445    DC01             david                           
SMB         10.129.203.121  445    DC01             IPC$            READ            Remote IPC
SMB         10.129.203.121  445    DC01             IT              READ,WRITE      
SMB         10.129.203.121  445    DC01             john                            
SMB         10.129.203.121  445    DC01             julio           READ,WRITE      
SMB         10.129.203.121  445    DC01             linux01         READ,WRITE      
SMB         10.129.203.121  445    DC01             NETLOGON        READ            Logon server share 
SMB         10.129.203.121  445    DC01             svc_workstations                 
SMB         10.129.203.121  445    DC01             SYSVOL          READ            Logon server share 
SMB         10.129.203.121  445    DC01             Users
```

{% hint style="info" %}
**Note:** At the time of writing this module, CrackMapExec doesn't support querying multiple usernames and passwords with the `--shares` option.
{% endhint %}

The option `--shares` displays each share on the target machine and which permissions (READ/WRITE) our user has on it. We have read and write access to an interesting folder called `IT`. We can easily open it using [Impacket smbclient](https://github.com/SecureAuthCorp/impacket/blob/master/examples/smbclient.py), or we can mount the share. It can become inconvenient when checking the content of hundreds of shares. For this purpose, CrackMapExec comes with two great features the `spider` option and the module `spider_plus`.

{% hint style="info" %}
**Note:** Keep in mind that any computer in the domain can have a shared folder. We should target any previously identified machines on the network we are targeting to find shared folders.
{% endhint %}

***

## The Spider Option

The `--spider` option in CrackMapExec allows you to search in a remote share and find juicy files depending on what you are looking for. For example, we add the option `--pattern` followed by the word we want to look for, in this case, `txt`, and we can list all files with `txt` on it (test.txt, atxtb.csv)

### **Using the Spider Option to Search for Files Containing "txt"**

```shell-session
$ crackmapexec smb 10.129.203.121 -u grace -p Inlanefreight01! --spider IT --pattern txt

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\grace:Inlanefreight01! 
SMB         10.129.203.121  445    DC01             [*] Started spidering
SMB         10.129.203.121  445    DC01             [*] Spidering .
SMB         10.129.203.121  445    DC01             //10.129.203.121/IT/Creds.txt [lastm:'2022-10-31 11:16' size:54]
SMB         10.129.203.121  445    DC01             //10.129.203.121/IT/IPlist.txt [lastm:'2022-10-31 11:15' size:36]

<SNIP>

SMB         10.129.203.121  445    DC01             [*] Done spidering (Completed in 1.7534186840057373)
```

We can also use regular expressions with the option `--regex [REGEX]` to do more granular searches on folders, file names, or file content. In the following example, let's use `--regex .` to display any file and directory in the shared folder `IT`:

### **List all Files and Directories in the IT Share**

```shell-session
$ crackmapexec smb 10.129.204.177 -u grace -p Inlanefreight01! --spider IT --regex .

SMB         10.129.204.177  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.204.177  445    DC01             [+] inlanefreight.htb\grace:Inlanefreight01! 
SMB         10.129.204.177  445    DC01             [*] Started spidering
SMB         10.129.204.177  445    DC01             [*] Spidering .
SMB         10.129.204.177  445    DC01             //10.129.204.177/IT/. [dir]
SMB         10.129.204.177  445    DC01             //10.129.204.177/IT/.. [dir]
SMB         10.129.204.177  445    DC01             //10.129.204.177/IT/Creds.txt [lastm:'2022-12-01 09:01' size:54]
SMB         10.129.204.177  445    DC01             //10.129.204.177/IT/Documents [dir]
SMB         10.129.204.177  445    DC01             //10.129.204.177/IT/IPlist.txt [lastm:'2022-12-01 09:01' size:36]
SMB         10.129.204.177  445    DC01             //10.129.204.177/IT/passwd [lastm:'2022-12-19 11:28' size:3456]
SMB         10.129.204.177  445    DC01             //10.129.204.177/IT/Documents/. [dir]
...SNIP...
SMB         10.129.204.177  445    DC01             [*] Done spidering (Completed in 1.593825340270996)
```

If we want to search file content, we need to enable it with the option `--content`. Let's search for a file containing the word "Encrypt."

### **Searching File Contents**

```shell-session
$ crackmapexec smb 10.129.204.177 -u grace -p Inlanefreight01! --spider IT --content --regex Encrypt

SMB         10.129.204.177  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.204.177  445    DC01             [+] inlanefreight.htb\grace:Inlanefreight01! 
SMB         10.129.204.177  445    DC01             [*] Started spidering
SMB         10.129.204.177  445    DC01             [*] Spidering .
SMB         10.129.204.177  445    DC01             //10.129.204.177/IT/Creds.txt [lastm:'2022-12-01 09:01' size:54 offset:54 regex:'b'Encrypt'']
SMB         10.129.204.177  445    DC01             [*] Done spidering (Completed in 3.5477945804595947)
```

We can see an interesting file called `Creds.txt`, which contains very juicy information. Using CrackMapExec, we can get a remote file. We need to specify the share using the option `--share <SHARENAME>`, then use `--get-file` followed by the file's path within the share and set an output file name.

### **Retrieving a File in a Shared Folder**

```shell-session
$ crackmapexec smb 10.129.203.121 -u grace -p Inlanefreight01! --share IT --get-file Creds.txt Creds.txt

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\grace:Inlanefreight01! 
SMB         10.129.203.121  445    DC01             [*] Copy Creds.txt to Creds.txt
SMB         10.129.203.121  445    DC01             [+] File Creds.txt was transferred to Creds.txt
```

```shell-session
$ cat Creds.txt 

Creds Encrypted:
ZWxpZXNlcjpTdXBlckNvbXBsZXgwMTIxIzIK
```

In the opposite case, imagine we want to send a file to a remote share. We need to find a share where we have `WRITE` privileges. Then we can use the option `--put-file` as we did with the `--get-file`.

### **Sending a File to a Shared Folder**

```shell-session
$ crackmapexec smb 10.129.203.121 -u grace -p Inlanefreight01! --share IT --put-file /etc/passwd passwd

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\grace:Inlanefreight01! 
SMB         10.129.203.121  445    DC01             [*] Copy /etc/passwd to passwd
SMB         10.129.203.121  445    DC01             [+] Created file /etc/passwd on \\IT\passwd
```

{% hint style="info" %}
**Note:** If we are transferring a large file and it fails, make sure to try again. If you keep getting an error, try adding the option `--smb-timeout` with a value greater than the default two (2).
{% endhint %}

***

## The spider\_plus Module

Sometimes we can come across a share and want to quickly list all files regardless of the extension without mounting it or using `smbclient.py`. CrackMapExec comes with a module called `spider_plus` that will handle that. It will by default create a folder `/tmp/cme_spider_plus` and a JSON file `IP.json` containing the share and files information. We can use the module option `EXCLUDE_DIR` to prevent the tool from looking at shares like `IPC$`,`NETLOGON`,`SYSVOL`, etc.

### **Using the Module spider\_plus**

```shell-session
$ crackmapexec smb 10.129.203.121 -u grace -p 'Inlanefreight01!' -M spider_plus -o EXCLUDE_DIR=IPC$,print$,NETLOGON,SYSVOL

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\grace:Inlanefreight01! 
SPIDER_P... 10.129.203.121  445    DC01             [*] Started spidering plus with option:
SPIDER_P... 10.129.203.121  445    DC01             [*]        DIR: ['ipc$', 'print$', 'netlogon', 'sysvol']
SPIDER_P... 10.129.203.121  445    DC01             [*]        EXT: ['ico', 'lnk']
SPIDER_P... 10.129.203.121  445    DC01             [*]       SIZE: 51200
SPIDER_P... 10.129.203.121  445    DC01             [*]     OUTPUT: /tmp/cme_spider_plus
```

We can navigate to the directory and get a list of all files accessible to the user:

### **Listing Files Available to the User**

```shell-session
$ cat /tmp/cme_spider_plus/10.129.203.121.json 

{
    "IT": {
        "Creds.txt": {
            "atime_epoch": "2022-10-31 11:16:17",
            "ctime_epoch": "2022-10-31 11:15:17",
            "mtime_epoch": "2022-10-31 11:16:17",
            "size": "54 Bytes"
        },
        "IPlist.txt": {
            "atime_epoch": "2022-10-31 11:15:11",
            "ctime_epoch": "2022-10-31 11:14:52",
            "mtime_epoch": "2022-10-31 11:15:11",
            "size": "36 Bytes"
        }
    },
    "linux01": {
        "flag.txt": {
            "atime_epoch": "2022-10-05 10:17:02",
            "ctime_epoch": "2022-10-05 10:17:02",
            "mtime_epoch": "2022-10-11 11:44:14",
            "size": "52 Bytes"
        },
        "information-txt.csv": {
            "atime_epoch": "2022-10-31 15:00:58",
            "ctime_epoch": "2022-10-31 14:21:36",
            "mtime_epoch": "2022-10-31 15:00:58",
            "size": "284 Bytes"
        }
    }
}
```

If we want to download all the content of the share, we can use the option `READ_ONLY=false` as follow:

```shell-session
$ crackmapexec smb 10.129.203.121 -u grace -p Inlanefreight01! -M spider_plus -o EXCLUDE_DIR=ADMIN$,IPC$,print$,NETLOGON,SYSVOL READ_ONLY=false

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\grace:Inlanefreight01! 
SPIDER_P... 10.129.203.121  445    DC01             [*] Started spidering plus with option:
SPIDER_P... 10.129.203.121  445    DC01             [*]        DIR: ['ipc$', 'print$', 'netlogon', 'sysvol']
SPIDER_P... 10.129.203.121  445    DC01             [*]        EXT: ['ico', 'lnk']
SPIDER_P... 10.129.203.121  445    DC01             [*]       SIZE: 51200
SPIDER_P... 10.129.203.121  445    DC01             [*]     OUTPUT: /tmp/cme_spider_plus
```

```shell-session
$ ls -R /tmp/cme_spider_plus/10.129.203.121/
/tmp/cme_spider_plus/10.129.203.121/:
IT  linux01

/tmp/cme_spider_plus/10.129.203.121/IT:
Creds.txt  Documents  IPlist.txt

...SNIP... 

/tmp/cme_spider_plus/10.129.203.121/linux01:
flag.txt  information-txt.csv
```

{% hint style="info" %}
**Note:** We have to be patient. The process could take a few minutes, depending on the number of shared folders and files.
{% endhint %}

To view all options available for the `spider_plus` module, we can use `--options`:

### **Spider\_plus Options**

```shell-session
$ crackmapexec smb -M spider_plus --options

[*] spider_plus module options:

            READ_ONLY           Only list files and put the name into a JSON (default: True)
            EXCLUDE_EXTS        Extension file to exclude (Default: ico,lnk)
            EXCLUDE_DIR         Directory to exclude (Default: print$)
            MAX_FILE_SIZE       Max file size allowed to dump (Default: 51200)
            OUTPUT_FOLDER       Path of the remote folder where the dump will occur (Default: /tmp/cme_spider_plus)
```
