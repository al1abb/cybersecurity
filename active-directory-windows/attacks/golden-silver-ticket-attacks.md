# Golden / Silver Ticket Attacks

## Overview

A golden ticket attack works by dumping the ticket-granting ticket of any user on the domain this would preferably be a domain admin, however, for a golden ticket you would dump the **krbtgt ticket** and for a silver ticket, you would dump **any service or domain admin ticket**.&#x20;

This will provide you with the service/domain admin account's **SID or security identifier** that is a unique identifier for each user account, as well as the **NTLM hash**.&#x20;

You then use these details inside of a mimikatz golden ticket attack in order to create a TGT that impersonates the given service account information.
