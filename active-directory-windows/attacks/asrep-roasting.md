# ASREP Roasting

ASReproasting occurs when a user account has the privilege "Does not require Pre-Authentication" set.&#x20;

This means that the account does not need to provide valid identification before requesting a Kerberos Ticket on the specified user account.



{% hint style="info" %}
AS-REP Roasting dumps the krbasrep5 hashes of user accounts that have Kerberos pre-authentication disabled.&#x20;

Unlike Kerberoasting these users do not have to be service accounts the only requirement to be able to AS-REP roast a user is the user must have pre-authentication disabled.
{% endhint %}





Get ASReproastable accounts from the Key Distribution Center (using impacket)

```bash
impacket-GetNPUsers spookysec.local/svc-admin
```

