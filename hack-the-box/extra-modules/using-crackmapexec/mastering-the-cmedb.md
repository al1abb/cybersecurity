# Mastering the CMEDB

CME automatically stores all used/dumped credentials (along with other information) in its SQLite database, set up on the first run. All workspaces and relative databases are stored in `~/.cme/workspaces`. The default databases are located at `~/.cme/workspaces/default`. This directory has an SQLite file for each protocol.

### **Listing Default Databases**

```shell-session
$ ls ~/.cme/workspaces/default/

ftp.db  ldap.db  mssql.db  rdp.db  smb.db  ssh.db  winrm.db
```

***

## Interacting with the Database

CME ships with a second command-line script, `cmedb`, which facilitates interacting with the back-end database. Typing the command `cmedb` will drop us into a command shell:

### **CMEDB**

```shell-session
$ cmedb

cmedb (default) >
```

***

## Workspaces

The default workspace name is called `default` (as represented within the prompt). Once a workspace is selected, everything that we do in CME will be stored in that workspace. To create a workspace, we need to go to the root of the command prompt `cmedb (default) >`. If we are in the protocol database, we need to use the command `back`.

### **Creating a Workspace**

```shell-session
$ cmedb 
cmedb (default)(smb) > back
cmedb (default) > workspace create inlanefreight
[*] Creating workspace 'inlanefreight'
[*] Initializing FTP protocol database
[*] Initializing SSH protocol database
[*] Initializing RDP protocol database
[*] Initializing LDAP protocol database
[*] Initializing MSSQL protocol database
[*] Initializing SMB protocol database
[*] Initializing WINRM protocol database
cmedb (inlanefreight) >
```

To list workspaces, we can use `workspace list`, and to switch workspace, we type `workspace <workspace>`.

### **Listing and Switching Workspaces**

```shell-session
cmedb (inlanefreight) > workspace list

[*] Enumerating Workspaces
default
==> inlanefreight
cmedb (inlanefreight) > workspace default
cmedb (default) >
```

***

## Accessing a Protocol's Database

`cmedb` has a database for each protocol, but at the time of writing this module, only `SMB` and `MSSQL` have helpful options:

<table data-header-hidden><thead><tr><th width="106.727294921875"></th><th></th></tr></thead><tbody><tr><td><strong>Protocol</strong></td><td><strong>Options</strong></td></tr><tr><td>smb</td><td>back creds exit export groups hosts import shares</td></tr><tr><td>mssql</td><td>back creds exit export hosts import</td></tr><tr><td>ldap</td><td>back exit export import</td></tr><tr><td>winrm</td><td>back exit export import</td></tr><tr><td>rdp</td><td>back exit export import</td></tr><tr><td>ftp</td><td>back exit export import</td></tr><tr><td>ssh</td><td>back exit export import</td></tr></tbody></table>

To access a protocol's database, run `proto <protocol>`. Within the protocol, we can use the option `help` to display the available options:

### **Connecting to the SMB Protocol Database**

```shell-session
cmedb (default) > proto smb
cmedb (default)(smb) > help

Documented commands (type help <topic>):
========================================
help

Undocumented commands:
======================
back  creds  exit  export  groups  hosts  import  shares
```

***

## Protocol Options

Every time we use the `SMB` or `MSSQL` protocol, the credentials, the hosts we attack, the shares we access, and the groups we enumerate are stored in the CrackMapExec database. Let's access the data we have in the database.

### Displaying Credentials

The CrackMapExec database stores all the credentials we have used or obtained using CrackMapExec. This database stores the type of credential, whether plaintext or hash, the domain, username, and password. To see the credentials for the SMB protocol, we need to use the option `creds` within the protocol.

### **Displaying SMB Credentials**

```shell-session
$ cmedb
cmedb (default)(smb) > creds

+Credentials---------+-----------+-----------------+--------------------------+-------------------------------------------------------------------+
| CredID | Admin On  | CredType  | Domain          | UserName                 | Password                                                          |
+--------+-----------+-----------+-----------------+--------------------------+-------------------------------------------------------------------+
| 1      | 0 Host(s) | plaintext | INLANEFREIGHT   | peter                    | Password123                                                       |
| 2      | 2 Host(s) | plaintext | INLANEFREIGHT   | robert                   | Inlanefreight01!                                                  |
| 3      | 0 Host(s) | plaintext | INLANEFREIGHT   | grace                    | Inlanefreight01!                                                  |
| 4      | 4 Host(s) | plaintext | INLANEFREIGHT   | julio                    | Password1                                                         |

<SNIP>

| 40     | 0 Host(s) | hash      | INLANEFREIGHT   | kiosko                   | aad3b435b51404eeaad3b435b51404ee:f399c1b9e7f851b949767163c35ae296 |
| 41     | 0 Host(s) | hash      | INLANEFREIGHT   | testaccount              | aad3b435b51404eeaad3b435b51404ee:e02ca966c5c0b22eba3c8c4c5ae568b1 |
+--------+-----------+-----------+-----------------+--------------------------+-------------------------------------------------------------------+

cmedb (default)(smb) > creds julio

+Credentials---------+-----------+---------------+----------+----------------------------------+
| CredID | Admin On  | CredType  | Domain        | UserName | Password                         |
+--------+-----------+-----------+---------------+----------+----------------------------------+
| 4      | 4 Host(s) | plaintext | INLANEFREIGHT | julio    | Password1                        |
| 26     | 0 Host(s) | hash      | INLANEFREIGHT | julio    | 64f12cddaa88057e06a81b54e73b949b |
+--------+-----------+-----------+---------------+----------+----------------------------------+
```

As you see, we can also query specific users by adding a username after `creds`. We can also list all hashes with the option `creds hash` or all plaintext credentials with the option `creds plaintext`.

### **Displaying Hashes and Plaintext Credentials**

```shell-session
cmedb (default)(smb) > creds hash

+Credentials---------+----------+---------------+--------------------+-------------------------------------------------------------------+
| CredID | Admin On  | CredType | Domain        | UserName           | Password                                                          |
+--------+-----------+----------+---------------+--------------------+-------------------------------------------------------------------+

<SNIP>

| 24     | 0 Host(s) | hash     | DC01          | Guest              | aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0 |
| 25     | 0 Host(s) | hash     | DC01          | DefaultAccount     | aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0 |
| 26     | 0 Host(s) | hash     | INLANEFREIGHT | julio              | 64f12cddaa88057e06a81b54e73b949b                                  |

<SNIP>

+--------+-----------+----------+---------------+--------------------+-------------------------------------------------------------------+

cmedb (default)(smb) > creds plaintext

+Credentials---------+-----------+-----------------+--------------------------+----------------------------------+
| CredID | Admin On  | CredType  | Domain          | UserName                 | Password                         |
+--------+-----------+-----------+-----------------+--------------------------+----------------------------------+
| 1      | 0 Host(s) | plaintext | INLANEFREIGHT   | peter                    | Password123                      |
| 2      | 2 Host(s) | plaintext | INLANEFREIGHT   | robert                   | Inlanefreight01!                 |
| 3      | 0 Host(s) | plaintext | INLANEFREIGHT   | grace                    | Inlanefreight01!                 |

<SNIP>

+--------+-----------+-----------+-----------------+--------------------------+----------------------------------+
```

{% hint style="info" %}
**Note:** cmedb allows tab auto-completion to display available options.
{% endhint %}

MSSQL credentials are saved in the `MSSQL` protocol and can be displayed as we display `SMB` credentials.

### **Displaying Credentials for MSSQL**

```shell-session
$ cmedb
cmedb (default)(smb) > back
cmedb (default) > proto mssql
cmedb (default)(mssql) > creds

+Credentials---------+-----------+---------------+---------------+----------------------------------+
| CredID | Admin On  | CredType  | Domain        | UserName      | Password                         |
+--------+-----------+-----------+---------------+---------------+----------------------------------+
| 1      | 0 Host(s) | plaintext | DC01          | nicole        | Database2                        |

<SNIP>

| 6      | 0 Host(s) | hash      | INLANEFREIGHT | julio         | 64F12CDDAA88057E06A81B54E73B949B |
| 7      | 0 Host(s) | plaintext | INLANEFREIGHT | julio         | Password1                        |
+--------+-----------+-----------+---------------+---------------+----------------------------------+
```

{% hint style="info" %}
**Note:** If we see the Domain field with a computer, it means that we are using an MSSQL account.
{% endhint %}

### Using Credentials

We can also use credentials IDs from the database to execute CrackMapExec. We need to identify the credentials we want to use and determine which id is associated with the account. Let's use julio's credentials with id 4. To use a credential id instead of a username and password, we need to use the option `-id <CredID>`.

### **Using CredID to Interact with CrackMapExec**

```shell-session
$ crackmapexec smb 10.129.203.121 -id 4 -x whoami

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] INLANEFREIGHT\julio:Password1 (Pwn3d!)
SMB         10.129.203.121  445    DC01             [+] Executed command 
SMB         10.129.203.121  445    DC01             inlanefreight\julio
```

### Hosts Information

For `MSSQL` and `SMB`, we can also identify the computers to which we have gained access, their IP, domain, and operating system.

### **Displaying Hosts**

```shell-session
$ cmedb
cmedb (default)(smb) > hosts

+Hosts---+-----------+--------------------------------+-----------------+-----------------+--------------------------+-------+---------+
| HostID | Admins    | IP                             | Hostname        | Domain          | OS                       | SMBv1 | Signing |
+--------+-----------+--------------------------------+-----------------+-----------------+--------------------------+-------+---------+
| 1      | 1 Cred(s) | 10.129.203.121                 | DC01            | INLANEFREIGHT   | Windows 10.0 Build 17763 | 0     | 1       |
| 2      | 3 Cred(s) | 10.129.204.23                  | MS01            | INLANEFREIGHT   | Windows 10.0 Build 17763 | 0     | 0       |

<SNIP>

| 9      | 1 Cred(s) | dead:beef::8c8a:5209:5876:537d | MS01            | INLANEFREIGHT   | Windows 10.0 Build 17763 | 0     | 0       |
| 11     | 0 Cred(s) | dc01.inlanefreight.htb         | DC01            | INLANEFREIGHT   | Windows 10.0 Build 17763 | 0     | 1       |
+--------+-----------+--------------------------------+-----------------+-----------------+--------------------------+-------+---------+

cmedb (default)(smb) > hosts ms01

+Hosts---+-----------+--------------------------------+----------+---------------+--------------------------+-------+---------+
| HostID | Admins    | IP                             | Hostname | Domain        | OS                       | SMBv1 | Signing |
+--------+-----------+--------------------------------+----------+---------------+--------------------------+-------+---------+
| 2      | 3 Cred(s) | 10.129.204.23                  | MS01     | INLANEFREIGHT | Windows 10.0 Build 17763 | 0     | 0       |
| 9      | 1 Cred(s) | dead:beef::8c8a:5209:5876:537d | MS01     | INLANEFREIGHT | Windows 10.0 Build 17763 | 0     | 0       |
+--------+-----------+--------------------------------+----------+---------------+--------------------------+-------+---------+
```

### Share Information

The CME database also stores the shared folders we have identified, and it tells us if we have users with read and write access. To access the shares information, we need to use the option `shares` within the SMB protocol in cmedb.

### **Retrieving Shares**

```shell-session
$ cmedb
cmedb (default)(smb) > shares

+---------+----------+------------------+---------------------------------------------+-------------+--------------+
| ShareID | computer | Name             | Remark                                      | Read Access | Write Access |
+---------+----------+------------------+---------------------------------------------+-------------+--------------+
| 1       | DC01     | ADMIN$           | Remote Admin                                | 0 User(s)   | 0 Users      |
| 2       | DC01     | C$               | Default share                               | 0 User(s)   | 0 Users      |
| 3       | DC01     | carlos           |                                             | 0 User(s)   | 0 Users      |

<SNIP>

| 33      | MS01     | ADMIN$           | Remote Admin                                | 1 User(s)   | 1 Users      |
| 34      | MS01     | C$               | Default share                               | 1 User(s)   | 1 Users      |
| 35      | MS01     | CertEnroll       | Active Directory Certificate Services share | 1 User(s)   | 1 Users      |
+---------+----------+------------------+---------------------------------------------+-------------+--------------+
```

***

## Adding and Removing Users

CME supports the ability to add or remove users from the database manually. We select the protocol (SMBor MSSQL) and use `creds add` or `creds remove`.

### **Adding a User to cmedb**

```shell-session
$ cmedb
cmedb (default)(smb) > creds add
[!] Format is 'add domain username password
cmedb (default)(smb) > creds add INLANEFREIGHT john Password3                                                                                      
cmedb (default)(smb) > creds

+Credentials---------+-----------+-----------------+--------------------------+-------------------------------------------------------------------+
| CredID | Admin On  | CredType  | Domain          | UserName                 | Password                                                          |
+--------+-----------+-----------+-----------------+--------------------------+-------------------------------------------------------------------+

<SNIP>

| 45     | 0 Host(s) | plaintext | INLANEFREIGHT   | john                     | Password3                                                         |
+--------+-----------+-----------+-----------------+--------------------------+-------------------------------------------------------------------+
```

Now we can try to remove the user we added.

### **Removing a User from cmedb**

```shell-session
$ cmedb
cmedb (default)(smb) > creds remove
[!] Format is 'remove <credID>'
cmedb (default)(smb) > creds 45

+Credential(s)-------+----------------------+---------------+----------+-----------+
| CredID | CredType  | Pillaged From HostID | Domain        | UserName | Password  |
+--------+-----------+----------------------+---------------+----------+-----------+
| 45     | plaintext | None                 | INLANEFREIGHT | john     | Password3 |
+--------+-----------+----------------------+---------------+----------+-----------+

<SNIP>

cmedb (default)(smb) > creds remove 45
cmedb (default)(smb) > creds 45

+Credentials--------+----------+--------+----------+----------+
| CredID | Admin On | CredType | Domain | UserName | Password |
+--------+----------+----------+--------+----------+----------+
```

***

## Importing Empire Credentials

Another feature that cmedb has is the ability to import credentials from `Empire`.

### **Import from Empire**

```shell-session
$ cmedb
cmedb (default)(smb) > import empire 
[-] Unable to connect to Empire's RESTful API server: HTTPSConnectionPool(host='127.0.0.1', port=1337): Max retries exceeded with url: /api/admin/login (Caused by NewConnectionError('<urllib3.connection.HTTPSConnection object at 0x7f5d248fabe0>: Failed to establish a new connection: [Errno 111] Connection refused'))
```

{% hint style="info" %}
**Note:** Make sure to configure Empire if you want to use this feature.
{% endhint %}

***

## Export cmedb Data

We can export credentials, hosts, local admins, and shares from the CrackMapExec database.

### **Exporting Credentials from cmedb**

```shell-session
$ cmedb
cmedb (default)(smb) > export invalid
[-] invalid argument, specify creds, hosts, local_admins, or shares
cmedb (default)(smb) > export creds
[-] invalid arguments, export creds <simple/detailed> <filename>
cmedb (default)(smb) > export creds detailed detailed_creds.csv
[+] creds exported
cmedb (default)(smb) > export shares detailed detailed_shares.csv
[+] creds exported
cmedb (default)(smb) > export local_admins detailed detailed_local_admins.csv
[+] Local Admins exported
cmedb (default)(smb) > exit
```

```shell-session
$ cat detailed_local_admins.csv 
"id";"userid";"computer"
"1";"INLANEFREIGHT/julio";"DC01"
"2";"INLANEFREIGHT/julio";"MS01"

<SNIP>
```

Data is exported as a CSV file. We can open it using tools such as LibreOffice or Excel.

{% embed url="https://academy.hackthebox.com/storage/modules/84/libreoffice.jpg" %}
