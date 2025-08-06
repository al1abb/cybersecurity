# Popular Modules

One of the most exciting things about CrackMapExec is that it is modular and allows anyone to create modules and contribute them to the tool. CrackMapExec has more than 50 modules that enable us to perform operations to facilitate exploitation and post-exploitation tasks. This section will review some of these modules for the `LDAP` and `SMB` protocols.

***

## LDAP Protocol Modules

The LDAP protocol commonly allows us to interact with Domain Controllers and obtain information from them. Let's review some modules that will enable us to extract interesting information from Active Directory.

### LDAP Module - get-network

The module `get-network` is based on the [Active Directory Integrated DNS dump](https://github.com/dirkjanm/adidnsdump). By default, any user in Active Directory can enumerate all DNS records in the Domain or Forest DNS zones, similar to a zone transfer. This tool enables the enumeration and exporting of all DNS records in the zone for recon purposes of internal networks.

We have three (3) ways to use the module: Retrieve only the IP address. Retrieve only the domain names. Retrieve both (IP and domain names).

By default, if we don't specify any option, the module will retrieve only the IP address. If we choose the option `ALL=true`, it will retrieve both IP and domain names, and if we specify `ONLY_HOSTS=true`, we will only retrieve the FQDN.

### **Getting Records from the DNS Server**

```shell-session
$ crackmapexec ldap dc01.inlanefreight.htb -u julio -p Password1 -M get-network --options

[*] get-network module options:

            ALL      Get DNS and IP (default: false)
            ONLY_HOSTS    Get DNS only (no ip) (default: false)
```

```shell-session
$ crackmapexec ldap dc01.inlanefreight.htb -u julio -p Password1 -M get-network -o ALL=true

SMB         dc01.inlanefreight.htb 445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
LDAP        dc01.inlanefreight.htb 389    DC01             [+] inlanefreight.htb\julio:Password1 (Pwn3d!)
GET-NETW... dc01.inlanefreight.htb 389    DC01             [*] Querying zone for records
GET-NETW... dc01.inlanefreight.htb 389    DC01             [*] Using System DNS to resolve unknown entries. Make sure resolving your target domain works here or specify an IP as target host to use that server for queries
GET-NETW... dc01.inlanefreight.htb 389    DC01             Found 4 records
GET-NETW... dc01.inlanefreight.htb 389    DC01             [+] Dumped 4 records to /home/plaintext/.cme/logs/inlanefreight.htb_network_2022-12-13_065607.log
```

```shell-session
$ cat /home/plaintext/.cme/logs/inlanefreight.htb_network_2022-11-10_101113.log

dc01.inlanefreight.htb   10.129.203.121
test.inlanefreight.htb   172.16.1.39
database01.inlanefreight.htb     172.16.1.29
```

{% hint style="info" %}
**Note:** At the time of writing, the module has some differences with the `adidnsdump` tool. Results may be different from one account to another.
{% endhint %}

### **LAPS Module Retrieving Passwords**

```shell-session
$ crackmapexec ldap dc01.inlanefreight.htb -u robert -p Inlanefreight01! -M laps --options

[*] laps module options:

            COMPUTER    Computer name or wildcard ex: WIN-S10, WIN-* etc. Default: *
```

```shell-session
$ crackmapexec ldap dc01.inlanefreight.htb -u robert -p Inlanefreight01! -M laps
SMB         dc01.inlanefreight.htb 445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
LDAP        dc01.inlanefreight.htb 389    DC01             [+] inlanefreight.htb\robert:Inlanefreight01! (Pwn3d!)
LAPS        dc01.inlanefreight.htb 389    DC01             [*] Getting LAPS Passwords
LAPS        dc01.inlanefreight.htb 389    DC01             Computer: MS01$                Password: 7*vp5Nc8Ph7uR&
```

{% hint style="info" %}
**Note:** The password used is an example. It will not work against the target host.
{% endhint %}

### LDAP Module - MAQ

The Machine Account Quota (MAQ), represented by the attribute [MS-DS-Machine-Account-Quota](https://learn.microsoft.com/en-us/windows/win32/adschema/a-ms-ds-machineaccountquota), is a domain level attribute that by default indicates the number of computer accounts that a user is allowed to create in a domain.

There are a few attacks such as [Resource Based Constrained Delegation](https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/resource-based-constrained-delegation-ad-computer-object-take-over-and-privilged-code-execution) that requires us to create a machine in the domain, and this is why it's essential to enumerate the account machine quota attribute.

### **Machine Quota Module**

```shell-session
$ crackmapexec ldap dc01.inlanefreight.htb -u robert -p 'Inlanefreight01!' -M maq

SMB         dc01.inlanefreight.htb 445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
LDAP        dc01.inlanefreight.htb 389    DC01             [+] inlanefreight.htb\robert:Inlanefreight01! (Pwn3d!)
MAQ         dc01.inlanefreight.htb 389    DC01             [*] Getting the MachineAccountQuota
MAQ         dc01.inlanefreight.htb 389    DC01             MachineAccountQuota: 6
```

### LDAP Module - daclread

Another great module is `daclread`, which allows us to read and export the [DACLs](https://learn.microsoft.com/en-us/windows/win32/secauthz/dacls-and-aces) of one or multiple objects. This module will enable us to enumerate Active Directory access. It has the following options:

### **daclread Module Options**

```shell-session
$ crackmapexec ldap -M daclread --options

[*] daclread module options:

        Be carefull, this module cannot read the DACLS recursively. For example, if an object has particular rights because it belongs to a group, the module will not be able to see it directly, you have to check the group rights manually.
        TARGET          The objects that we want to read or backup the DACLs, sepcified by its SamAccountName
        TARGET_DN       The object that we want to read or backup the DACL, specified by its DN (usefull to target the domain itself)
        PRINCIPAL       The trustee that we want to filter on
        ACTION          The action to realise on the DACL (read, backup)
        ACE_TYPE        The type of ACE to read (Allowed or Denied)
        RIGHTS          An interesting right to filter on ('FullControl', 'ResetPassword', 'WriteMembers', 'DCSync')
        RIGHTS_GUID     A right GUID that specify a particular rights to filter on
```

Let's say we want to read all ACEs of the grace account. We can use the option `TARGET`, and the `ACTION` read:

### **Read Grace User's DACL**

```shell-session
$ crackmapexec ldap dc01.inlanefreight.htb -u '' -p '' -M daclread -o TARGET=grace ACTION=read

SMB         dc01.inlanefreight.htb 445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
LDAP        dc01.inlanefreight.htb 389    DC01             [+] inlanefreight.htb\:             
DACLREAD    dc01.inlanefreight.htb 389    DC01             Be carefull, this module cannot read the DACLS recursively.
DACLREAD    dc01.inlanefreight.htb 389    DC01             Target principal found in LDAP (CN=grace,CN=Users,DC=inlanefreight,DC=htb)
[*]  ACE[1] info                                                                               
[*]    ACE Type                  : ACCESS_ALLOWED_OBJECT_ACE                                   
[*]    ACE flags                 : None                                                        
[*]    Access mask               : ReadProperty                                                
[*]    Flags                     : ACE_OBJECT_TYPE_PRESENT                                     
[*]    Object type (GUID)        : User-Account-Restrictions (4c164200-20c0-11d0-a768-00aa006e0529)                                                                                           
[*]    Trustee (SID)             : RAS and IAS Servers (S-1-5-21-3325992272-2815718403-617452758-553)                                                                                         
[*]  ACE[2] info                                                                               
[*]    ACE Type                  : ACCESS_ALLOWED_OBJECT_ACE                                   
[*]    ACE flags                 : None                                                        
[*]    Access mask               : ReadProperty                                                
[*]    Flags                     : ACE_OBJECT_TYPE_PRESENT                                     
[*]    Object type (GUID)        : User-Logon (5f202010-79a5-11d0-9020-00c04fc2d4cf)           
[*]    Trustee (SID)             : RAS and IAS Servers (S-1-5-21-3325992272-2815718403-617452758-553)

<SNIP>
```
