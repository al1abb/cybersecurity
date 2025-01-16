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

Different `DNS records` are used for the DNS queries, which all have various tasks. Moreover, separate entries exist for different functions since we can set up mail servers and other servers for a domain.

<table data-header-hidden><thead><tr><th width="222"></th><th></th></tr></thead><tbody><tr><td><strong>DNS Record</strong></td><td><strong>Description</strong></td></tr><tr><td><code>A</code></td><td>Returns an IPv4 address of the requested domain as a result.</td></tr><tr><td><code>AAAA</code></td><td>Returns an IPv6 address of the requested domain.</td></tr><tr><td><code>MX</code></td><td>Returns the responsible mail servers as a result.</td></tr><tr><td><code>NS</code></td><td>Returns the DNS servers (nameservers) of the domain.</td></tr><tr><td><code>TXT</code></td><td>This record can contain various information. The all-rounder can be used, e.g., to validate the Google Search Console or validate SSL certificates. In addition, SPF and DMARC entries are set to validate mail traffic and protect it from spam.</td></tr><tr><td><code>CNAME</code></td><td>This record serves as an alias for another domain name. If you want the domain www.hackthebox.eu to point to the same IP as hackthebox.eu, you would create an A record for hackthebox.eu and a CNAME record for www.hackthebox.eu.</td></tr><tr><td><code>PTR</code></td><td>The PTR record works the other way around (reverse lookup). It converts IP addresses into valid domain names.</td></tr><tr><td><code>SOA</code></td><td>Provides information about the corresponding DNS zone and email address of the administrative contact.</td></tr></tbody></table>

The `SOA` record is located in a domain's zone file and specifies who is responsible for the operation of the domain and how DNS information for the domain is managed.

```shell-session
[!bash!]$ dig soa www.inlanefreight.com

; <<>> DiG 9.16.27-Debian <<>> soa www.inlanefreight.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 15876
;; flags: qr rd ra; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;www.inlanefreight.com.         IN      SOA

;; AUTHORITY SECTION:
inlanefreight.com.      900     IN      SOA     ns-161.awsdns-20.com. awsdns-hostmaster.amazon.com. 1 7200 900 1209600 86400

;; Query time: 16 msec
;; SERVER: 8.8.8.8#53(8.8.8.8)
;; WHEN: Thu Jan 05 12:56:10 GMT 2023
;; MSG SIZE  rcvd: 128
```

The dot (.) is replaced by an at sign (@) in the email address. In this example, the email address of the administrator is `awsdns-hostmaster@amazon.com`.

## Default Configuration

