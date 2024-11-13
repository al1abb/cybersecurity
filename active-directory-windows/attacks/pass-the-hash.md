# Pass the Hash

**Pass the ticket** works by dumping the TGT from the LSASS memory of the machine.&#x20;

**The Local Security Authority Subsystem Service (LSASS)** is a memory process that stores credentials on an active directory server and can store Kerberos ticket along with other credential types to act as the gatekeeper and accept or reject the credentials provided.&#x20;

You can dump the Kerberos Tickets from the LSASS memory just like you can dump hashes.&#x20;

When you dump the tickets with mimikatz it will give us a .kirbi ticket which can be used to gain domain admin if a domain admin ticket is in the LSASS memory.&#x20;

This attack is great for privilege escalation and lateral movement if there are unsecured domain service account tickets laying around.&#x20;

The attack allows you to escalate to domain admin if you dump a domain admin's ticket and then impersonate that ticket using mimikatz PTT attack allowing you to act as that domain admin.&#x20;

You can think of a pass the ticket attack like reusing an existing ticket we're not creating or destroying any tickets here we're simply reusing an existing ticket from another user on the domain and impersonating that ticket.
