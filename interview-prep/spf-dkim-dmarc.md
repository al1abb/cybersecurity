# SPF, DKIM, DMARC

* **Sender Policy Framework (SPF)** records specify which IP addresses or hostnames are allowed to send email on behalf of the domain.
* **DomainKeys Identified Mail (DKIM)** provides a way to cryptographically sign emails to ensure their authenticity. These records are often published as TXT records in the DNS. The public keys can be analyzed to discover the signing domain and other important information.
* **Domain-based Message Authentication, Reporting, and Conformance (DMARC)** records define the policy for handling emails that fail SPF or DKIM checks. These records are also published as TXT records and can be analyzed to reveal the domain's policy on what to do with failed emails, the reporting email address, and other configuration details.
