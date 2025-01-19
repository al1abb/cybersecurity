# whois

WHOIS is a widely used query and response protocol designed to access databases that store information about registered internet resources. Primarily associated with domain names, WHOIS can also provide details about IP address blocks and autonomous systems. Think of it as a giant phonebook for the internet, letting you look up who owns or is responsible for various online assets.

```shell-session
al1abb@htb[/htb]$ whois inlanefreight.com

[...]
Domain Name: inlanefreight.com
Registry Domain ID: 2420436757_DOMAIN_COM-VRSN
Registrar WHOIS Server: whois.registrar.amazon
Registrar URL: https://registrar.amazon.com
Updated Date: 2023-07-03T01:11:15Z
Creation Date: 2019-08-05T22:43:09Z
[...]
```

Each WHOIS record typically contains the following information:

* `Domain Name`: The domain name itself (e.g., example.com)
* `Registrar`: The company where the domain was registered (e.g., GoDaddy, Namecheap)
* `Registrant Contact`: The person or organization that registered the domain.
* `Administrative Contact`: The person responsible for managing the domain.
* `Technical Contact`: The person handling technical issues related to the domain.
* `Creation and Expiration Dates`: When the domain was registered and when it's set to expire.
* `Name Servers`: Servers that translate the domain name into an IP address.

## History of WHOIS

The history of WHOIS is intrinsically linked to the vision and dedication of [Elizabeth Feinler](https://en.wikipedia.org/wiki/Elizabeth_J._Feinler), a computer scientist who played a pivotal role in shaping the early internet.

In the 1970s, Feinler and her team at the Stanford Research Institute's Network Information Center (NIC) recognised the need for a system to track and manage the growing number of network resources on the ARPANET, the precursor to the modern internet. Their solution was the creation of the WHOIS directory, a rudimentary yet groundbreaking database that stored information about network users, hostnames, and domain names.

Click to expand on an interesting bit of internet history if you are interested

<details>

<summary>Click to expand on an interesting bit of internet history if you are interested</summary>

#### Formalisation and Standardization

As the internet expanded beyond its academic origins, the WHOIS protocol was formalised and standardized in `RFC 812`, published in 1982. This laid the groundwork for a more structured and scalable system to manage domain registration and technical details. Ken Harrenstien and Vic White, also at the NIC, played a crucial role in defining the WHOIS protocol and its query-response mechanisms.

#### The Rise of Distributed WHOIS and RIRs

With the internet's exponential growth, the centralised WHOIS model proved inadequate. The establishment of Regional Internet Registries (RIRs) in the 1990s marked a shift towards a distributed WHOIS system.

Key figures like Randy Bush and John Postel contributed to the development of the RIR system, which divided the responsibility of managing internet resources into regional zones. This decentralisation improved scalability and resilience, allowing WHOIS to keep pace with the internet's rapid expansion.

#### ICANN and the Modernization of WHOIS

The formation of the `Internet Corporation for Assigned Names and Numbers` (`ICANN`) in 1998 ushered in a new era for WHOIS. Vint Cerf, often referred to as one of the "fathers of the internet," played a crucial role in establishing ICANN, which assumed responsibility for global DNS management and WHOIS policy development.

This centralized oversight helped to standardize WHOIS data formats, improve accuracy, and resolve domain disputes arising from issues like cybersquatting, trademark infringement, or conflicts over unused domains. ICANN's Uniform Domain-Name Dispute-Resolution Policy (UDRP) provides a framework for resolving such conflicts through arbitration.

#### Privacy Concerns and the GDPR Era

The 21st century brought heightened awareness of privacy concerns related to WHOIS data. The public availability of personal information like names, addresses, and phone numbers became a growing concern. This led to the rise of privacy services that allowed domain owners to mask their personal information.

The implementation of the `General Data Protection Regulation` (`GDPR`) in 2018 further accelerated this trend, requiring WHOIS operators to comply with strict data protection rules.

Today, WHOIS continues to evolve in response to the ever-changing landscape of the internet. The tension between transparency and privacy remains a central theme. Efforts are underway to strike a balance through initiatives like the `Registration Data Access Protocol` (`RDAP`), which offers a more granular and privacy-conscious approach to accessing domain registration data.

</details>

## Why WHOIS Matters for Web Recon

WHOIS data serves as a treasure trove of information for penetration testers during the reconnaissance phase of an assessment. It offers valuable insights into the target organisation's digital footprint and potential vulnerabilities:

* `Identifying Key Personnel`: WHOIS records often reveal the names, email addresses, and phone numbers of individuals responsible for managing the domain. This information can be leveraged for social engineering attacks or to identify potential targets for phishing campaigns.
* `Discovering Network Infrastructure`: Technical details like name servers and IP addresses provide clues about the target's network infrastructure. This can help penetration testers identify potential entry points or misconfigurations.
* `Historical Data Analysis`: Accessing historical WHOIS records through services like [WhoisFreaks](https://whoisfreaks.com/) can reveal changes in ownership, contact information, or technical details over time. This can be useful for tracking the evolution of the target's digital presence.

## Utilizing WHOIS

Let's consider three scenarios to help illustrate the value of WHOIS data.

### Scenario 1: Phishing Investigation
