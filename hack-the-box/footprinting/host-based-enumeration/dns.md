# DNS

{% content-ref url="../../../interview-prep/dns-steps.md" %}
[dns-steps.md](../../../interview-prep/dns-steps.md)
{% endcontent-ref %}

## DNS

`Domain Name System` (`DNS`) is an integral part of the Internet. For example, through domain names, such as [academy.hackthebox.com](https://academy.hackthebox.com/) or [www.hackthebox.com](https://www.hackthebox.eu/), we can reach the web servers that the hosting provider has assigned one or more specific IP addresses. DNS is a system for resolving computer names into IP addresses, and it does not have a central database. Simplified, we can imagine it like a library with many different phone books. The information is distributed over many thousands of name servers. Globally distributed DNS servers translate domain names into IP addresses and thus control which server a user can reach via a particular domain. There are several types of DNS servers that are used worldwide:

* DNS root server
* Authoritative name server
* Non-authoritative name server
* Caching server
* Forwarding server
* Resolver

<table data-header-hidden><thead><tr><th width="169"></th><th></th></tr></thead><tbody><tr><td><strong>Server Type</strong></td><td><strong>Description</strong></td></tr><tr><td><code>DNS Root Server</code></td><td>The root servers of the DNS are responsible for the top-level domains (<code>TLD</code>). As the last instance, they are only requested if the name server does not respond. Thus, a root server is a central interface between users and content on the Internet, as it links domain and IP address. The <a href="https://www.icann.org/">Internet Corporation for Assigned Names and Numbers</a> (<code>ICANN</code>) coordinates the work of the root name servers. There are <code>13</code> such root servers around the globe.</td></tr><tr><td><code>Authoritative Nameserver</code></td><td>Authoritative name servers hold authority for a particular zone. They only answer queries from their area of responsibility, and their information is binding. If an authoritative name server cannot answer a client's query, the root name server takes over at that point.</td></tr><tr><td><code>Non-authoritative Nameserver</code></td><td>Non-authoritative name servers are not responsible for a particular DNS zone. Instead, they collect information on specific DNS zones themselves, which is done using recursive or iterative DNS querying.</td></tr><tr><td><code>Caching DNS Server</code></td><td>Caching DNS servers cache information from other name servers for a specified period. The authoritative name server determines the duration of this storage.</td></tr><tr><td><code>Forwarding Server</code></td><td>Forwarding servers perform only one function: they forward DNS queries to another DNS server.</td></tr><tr><td><code>Resolver</code></td><td>Resolvers are not authoritative DNS servers but perform name resolution locally in the computer or router.</td></tr></tbody></table>

DNS is mainly unencrypted. Devices on the local WLAN and Internet providers can therefore hack in and spy on DNS queries. Since this poses a privacy risk, there are now some solutions for DNS encryption. By default, IT security professionals apply `DNS over TLS` (`DoT`) or `DNS over HTTPS` (`DoH`) here. In addition, the network protocol `DNSCrypt` also encrypts the traffic between the computer and the name server.

{% hint style="info" %}
DNS does not only link computer names and IP addresses. It also stores and outputs additional information about the services associated with a domain. A DNS query can therefore also be used, for example, to determine which computer serves as the e-mail server for the domain in question or what the domain's name servers are called.
{% endhint %}

<figure><img src="../../../.gitbook/assets/image (73).png" alt=""><figcaption></figcaption></figure>

## DNS Records

