# Working with Modules

As we previously discussed, each CrackMapExec protocol supports modules. We can run `crackmapexec <protocol> -L` to view available modules for the specified protocol. Let's use the `LDAP` protocol as an example.

### **View Available Modules for LDAP**

```shell-session
$ crackmapexec ldap -L

[*] MAQ                       Retrieves the MachineAccountQuota domain-level attribute
[*] adcs                      Find PKI Enrollment Services in Active Directory and Certificate Templates Names
[*] daclread                  Read and backup the Discretionary Access Control List of objects. Based on the work of @_nwodtuhs and @BlWasp_. Be carefull, this module cannot read the DACLS recursively, more explains in the options.
[*] get-desc-users            Get description of the users. May contained password
[*] get-network               
[*] laps                      Retrieves the LAPS passwords
[*] ldap-checker              Checks whether LDAP signing and binding are required and / or enforced
[*] ldap-signing              Check whether LDAP signing is required
[*] subnets                   Retrieves the different Sites and Subnets of an Active Directory
[*] user-desc                 Get user descriptions stored in Active Directory
[*] whoami                    Get details of provided user
```

Let's pick the `user-desc` module and see how we can interact with modules.

{% hint style="info" %}
**Note:** Keep in mind that `LDAP` protocol communications won't work if we can't resolve the domain FQDN. If we are not connecting to the domain DNS servers, we need to configure the FQDN in the `/etc/hosts` file. Configure the target IP to the FQDN `dc01.inlanefreight.htb`.
{% endhint %}

***

## Identifying Options in Modules

A CrackMapExec module can have different options to set some custom values. There may be modules like `MAQ` which doesn't have any options, meaning that we can't modify its default action. To view a module's supported options, we can use the following syntax: `crackmapexec <protocol> -M <module_name> --options`

### **View Options for the LDAP MAQ Module**

```shell-session
$ crackmapexec ldap -M MAQ --options

[*] MAQ module options:
None
```

### View Options for the LDAP user-desc Module

```shell-session
$ crackmapexec ldap -M user-desc --options

[*] user-desc module options:

        LDAP_FILTER     Custom LDAP search filter (fully replaces the default search)
        DESC_FILTER     An additional seach filter for descriptions (supports wildcard *)
        DESC_INVERT     An additional seach filter for descriptions (shows non matching)
        USER_FILTER     An additional seach filter for usernames (supports wildcard *)
        USER_INVERT     An additional seach filter for usernames (shows non matching)
        KEYWORDS        Use a custom set of keywords (comma separated)
        ADD_KEYWORDS    Add additional keywords to the default set (comma separated)
```

The `LDAP` module `user-desc` will query all users in the Active Directory domain and retrieve their descriptions, it will only display the user's descriptions that match the default keywords, but it will save all descriptions in a file.

Default keywords are not provided in the description. If we want to know what those keywords are, we need to look at the source code. We can find the source code in the directory `CrackMapExec/cme/modules/`. Then we can look for the module name and open it. In our case, the Python script that contains the module `user-desc` is `user_description.py`. Let's grep the file to find the word `keywords`:

### **Looking at the Source Code of the user-desc Module**

```shell-session
$ cat CrackMapExec/cme/modules/user_description.py |grep keywords

        KEYWORDS        Use a custom set of keywords (comma separated)
        ADD_KEYWORDS    Add additional keywords to the default set (comma separated)
        self.keywords = {'pass', 'creds', 'creden', 'key', 'secret', 'default'}
        ...SNIP...
```

Let's use the module and find interesting user descriptions:

### **Retrieve User Description Using user-desc Module**

```shell-session
$ crackmapexec ldap dc01.inlanefreight.htb -u grace -p Inlanefreight01! -M user-desc 

SMB         dc01.inlanefreight.htb  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
LDAP        dc01.inlanefreight.htb  389    DC01             [+] inlanefreight.htb\grace:Inlanefreight01! 
USER-DES...                                                 User: krbtgt - Description: Key Distribution Center Service Account
USER-DES...                                                 User: alina - Description: Account for testing HR App. Password: HRApp123!
USER-DES...                                                 Saved 6 user descriptions to /home/plaintext/.cme/logs/UserDesc-10.129.203.121-20221031_120444.log
```

As we can see, it displays the description that contains the keywords `key` and `pass`, but all descriptions are saved in the log file.

### **Opening File with All Descriptions**

```shell-session
$ cat /home/plaintext/.cme/logs/UserDesc-10.129.203.121-20221031_120444.log

User:                     Description:
Administrator             Built-in account for administering the computer/domain
Guest                     Built-in account for guest access to the computer/domain
krbtgt                    Key Distribution Center Service Account
alina                     Account for testing HR App. Password: HRApp123!
engels                    Service Account for testing
testaccount               pwd: Testing123!
```

***

## Using a Module with Options

If we want to use a module option, we need to use the flag `-o`. All options are specified in the form of `KEY=value` (msfvenom style). In the following example, we will replace the default keywords and display values with the keywords `pwd` and `admin`.

### **Using user-desc with New Keywords**

```shell-session
$ crackmapexec ldap dc01.inlanefreight.htb -u grace -p Inlanefreight01! -M user-desc -o KEYWORDS=pwd,admin

SMB         dc01.inlanefreight.htb  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
LDAP        dc01.inlanefreight.htb  389    DC01             [+] inlanefreight.htb\grace:Inlanefreight01! 
USER-DES...                                                 User: Administrator - Description: Built-in account for administering the computer/domain
USER-DES...                                                 User: testaccount - Description: pwd: Testing123!
USER-DES...                                                 Saved 6 user descriptions to /home/plaintext/.cme/logs/UserDesc-10.129.203.121-20221031_123727.log
```

## Querying User Membership

`groupmembership` is another example of a module created during this training by an HTB Academy training developer, which allows us to query the groups to which a user belongs (we will discuss how to create your own modules later). To use it, we need to specify the user we want to query with the option `USER`.

There is a [PR](https://github.com/Porchetta-Industries/CrackMapExec/pull/696) for this module at the time of writing this training, but it can be used if downloaded and placed in the modules folder until it gets approved.

### **Querying Group Membership with a Custom Module**

```shell-session
al1abb@htb[/htb]$ cd CrackMapExec/cme/modules/
al1abb@htb[/htb]$ wget https://raw.githubusercontent.com/Porchetta-Industries/CrackMapExec/7d1e0fdaaf94b706155699223f984b6f9853fae4/cme/modules/groupmembership.py -q
al1abb@htb[/htb]$ crackmapexec ldap dc01.inlanefreight.htb -u grace -p Inlanefreight01! -M groupmembership -o USER=julio

SMB         dc01.inlanefreight.htb  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
LDAP        dc01.inlanefreight.htb  389    DC01             [+] inlanefreight.htb\grace:Inlanefreight01! 
GROUPMEM... dc01.inlanefreight.htb  389    DC01             [+] User: julio is member of following groups: 
GROUPMEM... dc01.inlanefreight.htb  389    DC01             Server Operators
GROUPMEM... dc01.inlanefreight.htb  389    DC01             Domain Admins
GROUPMEM... dc01.inlanefreight.htb  389    DC01             Domain Users
```
