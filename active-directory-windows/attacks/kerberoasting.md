# Kerberoasting

**Kerberoasting** allows a user to request a service ticket for any service with a registered SPN then use that ticket to crack the service password.

&#x20;If the service has a registered SPN then it can be Kerberoastable however the success of the attack depends on how strong the password is and if it is trackable as well as the privileges of the cracked service account.&#x20;

To enumerate Kerberoastable accounts I would suggest a tool like BloodHound to find all Kerberoastable accounts, it will allow you to see what kind of accounts you can kerberoast if they are domain admins, and what kind of connections they have to the rest of the domain.
