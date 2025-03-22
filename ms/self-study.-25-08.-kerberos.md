# Self-study. 25/08. Kerberos

## High-level overview:

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1)   (1).png" alt=""><figcaption><p>Kerberos Authentication Process</p></figcaption></figure>

**Steps 1 and 2** happen once, when the user logs on to their PC

**Steps 3 and 4** happen the first time they try to authenticate with the network service (SQL in this example). The service ticket they receive in Step 4 will get cached

**Step 5** happens every time they access the service and uses that cached ticket (until they log off or until the service ticket expires and then they need to repeat steps 3 and 4 again)&#x20;

## Network messages for each step

### Kerberos Initial Logon (Steps 1 and 2)

Client sends **AS-REQ**(1) and DC sends back **AS-REP**(2) containing user ticket (**TGT**)

We have **AS-REQ** and **AS-REP** (Steps 1 and 2)

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption><p>AS-REQ and AS-REP</p></figcaption></figure>

### Kerberos Service Ticket Request (Steps 3 and 4)

Client sends **TGS-REQ** (3) and DC sends back **TGS-REP** (4) containing service ticket

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption><p>TGS-REQ and TGS-REP</p></figcaption></figure>

### Kerberos Service Authentication (Step 5)

Client sends **AP-REQ** (5) to service (e.g SQL Server) including service ticket.

Service decrypts ticket and checks authenticator data against session key in ticket.

If the session key matches, it trusts that the user is who the ticket says they are.

<figure><img src="../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption><p>AP-REQ</p></figcaption></figure>

{% hint style="info" %}
Note that this message won’t usually be easy to see in network captures because it will be sent over whatever protocol the client communicates with the network service with (e.g HTTPS, SQL’s network protocol, or some proprietary protocol created just for this service)
{% endhint %}

## Golden Ticket Attack

A **golden ticket** in Active Directory grants the bearer **unlimited access**

A Golden Ticket attack abuses the Kerberos protocol, which depends on the use of shared secrets to encrypt and sign messages.&#x20;

One of these secrets is known only to the Key Distribution Center (KDC):  the password hash for the KRBTGT user, which is used to issue the Kerberos tickets required to access IT systems and data.&#x20;

Suppose an adversary compromises the KRBTGT password hash. In that case, they possess a golden ticket — they can mint Kerberos tickets as if they were Active Directory itself, giving them the power to access any resource they choose!&#x20;

Unfortunately, Golden Ticket attacks are extremely difficult to detect and respond to.

### How to do it?

You need to know 3 things:

1\) Domain - al1abb.local

2\) Domain SID (can be learned with `whoami /user`)

3\) KRBTGT - You need to be domain admin to get this. After that you can get it using mimikatz

{% hint style="info" %}
Golden Ticket does not expire for many years
{% endhint %}

{% embed url="https://player.vimeo.com/video/681319062" %}
Golden Ticket Attack Using Mimikatz
{% endembed %}

## Silver Ticket Attack

Silver Ticket attack involves compromising credentials and abusing the design of the [Kerberos](https://stealthbits.com/blog/what-is-kerberos/) protocol.&#x20;

A Silver Ticket only enables an attacker to forge ticket-granting service (TGS) tickets for **specific services**.&#x20;

TGS tickets are encrypted with the password hash for the service; therefore, if an adversary steals the hash for a service account, they can mint TGS tickets for that service.

While the scope of a Silver Ticket attack may be smaller, it is still a powerful tool in an adversary’s kit, enabling persistent and stealthy access to resources.&#x20;

Since only the service account’s password hash is required, it is also significantly easier to execute than a Golden Ticket attack.&#x20;

Techniques like harvesting hashes from LSASS.exe and **Kerberoasting** are common ways adversaries obtain service account password hashes.

### How to do it?

You need:

Password of a service account. (You can use Kerberoasting for this)

You take any account that has registered SPN (Service principle name) in AD and brute-force the password offline

{% embed url="https://player.vimeo.com/video/681322374" %}
Silver Ticket Attack Using Mimikatz
{% endembed %}

## Kerberoasting Attack

