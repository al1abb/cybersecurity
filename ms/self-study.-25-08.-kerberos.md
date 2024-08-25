# Self-study. 25/08. Kerberos

## High-level overview:

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption><p>Kerberos Authentication Process</p></figcaption></figure>

**Steps 1 and 2** happen once, when the user logs on to their PC

**Steps 3 and 4** happen the first time they try to authenticate with the network service (SQL in this example). The service ticket they receive in Step 4 will get cached

**Step 5** happens every time they access the service and uses that cached ticket (until they log off or until the service ticket expires and then they need to repeat steps 3 and 4 again)&#x20;

## Network messages for each step

### Kerberos Initial Logon (Steps 1 and 2)

Client sends **AS-REQ**(1) and DC sends back **AS-REP**(2) containing user ticket (**TGT**)

We have **AS-REQ** and **AS-REP** (Steps 1 and 2)

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption><p>AS-REQ and AS-REP</p></figcaption></figure>

### Kerberos Service Ticket Request (Steps 3 and 4)

Client sends **TGS-REQ** (3) and DC sends back **TGS-REP** (4) containing service ticket

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption><p>TGS-REQ and TGS-REP</p></figcaption></figure>

### Kerberos Service Authentication (Step 5)

Client sends **AP-REQ** (5) to service (e.g SQL Server) including service ticket.

Service decrypts ticket and checks authenticator data against session key in ticket.

If the session key matches, it trusts that the user is who the ticket says they are.

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption><p>AP-REQ</p></figcaption></figure>

{% hint style="info" %}
Note that this message won’t usually be easy to see in network captures because it will be sent over whatever protocol the client communicates with the network service with (e.g HTTPS, SQL’s network protocol, or some proprietary protocol created just for this service):
{% endhint %}
