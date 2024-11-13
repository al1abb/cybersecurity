# Kerberoasting

## Intro

**Kerberoasting** allows a user to request a service ticket for any service with a registered SPN then use that ticket to crack the service password.

&#x20;If the service has a registered SPN then it can be Kerberoastable however the success of the attack depends on how strong the password is and if it is trackable as well as the privileges of the cracked service account.&#x20;

To enumerate Kerberoastable accounts I would suggest a tool like BloodHound to find all Kerberoastable accounts, it will allow you to see what kind of accounts you can kerberoast if they are domain admins, and what kind of connections they have to the rest of the domain.

## Rubeus

Dump the Kerberos hash of any kerberoastable users:

```powershell
Rubeus.exe kerberoast
```

Now you can take the hash of the service and crack it (Hashcat mode 13100 for TGS-REP)

## Impacket

Dump the hash:

```bash
impacket-GetUserSPNs controller.local/Machine1:Password1 -dc-ip 10.10.2.205 -request
```

## What can a Service Account do?

After cracking the service account password there are various ways of exfiltrating data or collecting loot depending on whether the service account is a domain admin or not.&#x20;

If the service account is a domain admin you have control similar to that of a golden/silver ticket and can now gather loot such as dumping the NTDS.dit.&#x20;

If the service account is not a domain admin you can use it to log into other systems and pivot or escalate or you can use that cracked password to spray against other service and domain admin accounts; many companies may reuse the same or similar passwords for their service or domain admin users.

## Kerberoasting Mitigation

* &#x20;Strong Service Passwords - If the service account passwords are strong then kerberoasting will be ineffective
* &#x20;Don't Make Service Accounts Domain Admins - Service accounts don't need to be domain admins, kerberoasting won't be as effective if you don't make service accounts domain admins.
