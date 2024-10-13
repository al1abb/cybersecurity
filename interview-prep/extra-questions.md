# Extra questions

## Network (AZ)

SSL/TLS sertifikatının təhlükəsizlik üçün rolu nədir və necə işləyir?&#x20;

L2 attacklardan hansıları bilirsiniz?&#x20;

Firewall və IDS/IPS cihazları arasındakı fərq nədir? Onların təhlükəsizlikdə hansı rolu var?&#x20;

Threat İntelligence və SİEM toolunu qarışdırsaq, SOC analystə köməyi nə olar?&#x20;

Passive reconun qarşısını müəyyən qədər necə ala bilərik?&#x20;

Mac flood attackın digər adı nədir? (ARP flood) proses necə gedir və qarşısının alınması.&#x20;

DHCP starving attack və qarşısının alınması.&#x20;

SYN flood attack və qarşının alınması.&#x20;

DHCP spoofing attack və qarşısının alınması.&#x20;

VLAN hopping attack və qarşısının alınması.&#x20;

ARP attackların qarşısını necə alarsınız? Static, Port sec, Dynamic ARP&#x20;

SOA recordunun içərisində nələr olur?&#x20;

DORA prosesində Offer paketinin içərisində nələr olur?&#x20;

Mikroseqmentasiya nədir və nə üçün istifadə olunur?&#x20;

SOC mühitində Threat Hunting prosesi necə həyata keçirilir və hansı addımlar izlənir?

Hipotez Yaradılması -> Məlumatların Toplanması -> Məlumatların Analizi -> Suspicious dataların araşdırılması -> Response -> Report və sənədləşdirmə aparılması&#x20;

DMZ şəbəkə arxitekturasında necə istifadə olunur və hansı təhlükəsizlik risklərini azaldır?&#x20;

İOC və BİOC fərqləri&#x20;

EDR vs XDR vs Antivirus fərqləri

## MS (AZ)

MS:

1. Təsəvvür edin ki, hansısa şəbəkəyə RDP ilə qoşulursunuz, bu zaman hansı halda Kerberos, hansı halda NTLM authentication protokolundan istifadə edilir?
2. Active Directory-də Global Kataloq nədir və birdən çox domain-i olan mühitlərdə domain kontrollerlərin işinin effektivliyini necə artırır?
3. Elə bir ssenari danışın ki, forest, tree və domainlərdən hamısından istifadə etmək lazım olsun.
4. SSO, MFA, OTP anlayışları nədir, niyə istifadə olunur?
5. Active Directory-də "Sysvol" niyə önəmlidir və nə işə yarayır?
6. "Domain local", "global" və "universal" qruplar arasındakı fərq nədir və hər növü nə vaxt istifadə edərsən?
7. Pass-the-Hash attackı barədə danış və necə mitigate edərsən?
8. Hansı hallarda NTLM hələ də modern Active Directory-də istifadə olunur və necə bu protokolun istifadəsini minimuma salarsan?
9. NTLMv1 və v2 arasındakı fərq nədir?
10. Köhnə legacy sistemlərdə SSO prosesində NTLM-in rolundan danış və Kerberos əsaslı SSO-dan nə ilə fərqlənir?
11. Bütün Kerberos attackları (kerberoasting, As-rep roasting, golden, silver, diamond ticket) qısaca nə baş verdiyi prosesini danışın və hər birini necə detekt və mitigate edərsiniz?
12. ntds.dir və SAM barədə nə deyə bilərsiniz? SAM necə işləyir və AD databazasından fərqi nədir?
13. SPN (Service Principle Name) Kerberosda rolu nədir və düzgün konfiqurasiya olunmasa, hansı problemlərə yol aça bilər?
14. Workgroup və domain fərqi, konkret situasiya deyin ki nə vaxt hansını istifadə edərsiniz.
15. RADIUS serverin Windows mühitində rolu və AD ilə necə inteqrasiya olunur?

## Networking Questions (EN)

**1. What is the role of an SSL/TLS certificate for security and how does it work?** SSL/TLS certificates provide encryption for data in transit between a client and server, ensuring confidentiality and integrity. They use asymmetric encryption to establish a secure session, then switch to symmetric encryption for data exchange, protecting data from eavesdropping and tampering.

**2. Which Layer 2 attacks do you know?**

* MAC Flooding (CAM table overflow)
* ARP Spoofing (Poisoning)
* DHCP Spoofing
* VLAN Hopping

**3. What is the difference between firewall and IDS/IPS devices?**

* **Firewall**: Filters traffic based on pre-set rules, working as a barrier between trusted and untrusted networks.
* **IDS** (Intrusion Detection System): Monitors traffic for suspicious activities and alerts administrators but doesn’t block traffic.
* **IPS** (Intrusion Prevention System): Detects and blocks malicious traffic in real time.

**4. What will help the SOC analyst if we mix Threat Intelligence and SIEM tools?** Combining Threat Intelligence with a SIEM tool helps SOC analysts by enriching alerts with context, providing information on known threats, indicators of compromise (IOCs), and correlating external threat data with internal logs for faster detection and response.

**5. How can we prevent passive recon to a certain extent?**

* Use encryption (TLS, SSH) to prevent sniffing.
* Disable unnecessary services that expose information.
* Implement firewalls to block reconnaissance attempts (e.g., ICMP ping, port scanning).

**6. What is another name for a MAC flood attack?** Also called a **CAM table overflow attack**, it floods a switch’s CAM table with fake MAC addresses, causing it to behave like a hub and broadcast all traffic.

**Prevention**: Use **Port Security** to limit the number of MAC addresses per port and enable dynamic ARP inspection.

**7. What is a DHCP starvation attack and how to prevent it?** An attacker floods the DHCP server with DHCP requests, exhausting the IP pool, and preventing legitimate clients from obtaining IP addresses.

**Prevention**:

* Enable **DHCP Snooping**.
* Set rate limits for DHCP requests.

**8. What is a SYN flood attack and how to prevent it?** A DoS attack where an attacker sends numerous SYN requests but doesn't complete the handshake, exhausting the server’s resources.

**Prevention**:

* Implement **SYN Cookies**.
* Use **firewalls** and rate-limiting.

**9. What is DHCP spoofing and how to prevent it?** An attacker sets up a rogue DHCP server to assign incorrect IP settings to users, redirecting traffic.

**Prevention**:

* Enable **DHCP Snooping**.
* Use trusted ports for DHCP communication.

**10. What is VLAN hopping and how to prevent it?** An attack where the attacker sends traffic to a different VLAN by manipulating VLAN tagging.

**Prevention**:

* Disable **DTP (Dynamic Trunking Protocol)**.
* Configure access ports as access-only.

**11. How do you prevent ARP attacks?**

* Use **static ARP entries** where feasible.
* Enable **Port Security**.
* Use **Dynamic ARP Inspection (DAI)** to validate ARP packets.

**12. What is inside the SOA (Start of Authority) record?**

* Primary DNS server
* Email of the administrator
* Serial number
* Refresh, retry, and expiration times for zone updates.

**13. What is inside the Offer package during the DORA process?** The **DHCP Offer** contains:

* IP address offered
* Subnet mask
* Lease time
* Gateway, DNS server addresses (optional).

**14. What is microsegmentation and what is it used for?** Microsegmentation isolates workloads within a network using fine-grained security policies to limit lateral movement of threats. It’s used to enhance network security, especially in environments like data centers and cloud networks.

**15. How is the Threat Hunting process performed in a SOC environment?**

* **Hypothesis Generation**
* **Data Collection**
* **Data Analysis**
* **Suspicious Data Investigation**
* **Response**
* **Reporting and Documentation**

**16. How is DMZ used in network architecture and what security risks does it reduce?** A DMZ is a buffer zone between an internal network and untrusted external networks (like the internet), hosting services accessible to external users (e.g., web servers). It reduces the risk of exposing sensitive internal systems by isolating externally accessible resources.

**17. Differences between IOC and BIOC?**

* **IOC** (Indicator of Compromise): Detectable artifacts of a security breach (e.g., malicious IP, hash).
* **BIOC** (Behavioral Indicator of Compromise): Patterns of abnormal behavior indicative of a compromise (e.g., lateral movement, privilege escalation).

**18. EDR vs XDR vs Antivirus differences**

* **EDR**: Endpoint Detection and Response. Focuses on monitoring and responding to threats on endpoints.
* **XDR**: Extended Detection and Response. Aggregates data from multiple security layers (endpoint, network, cloud) for advanced threat detection.
* **Antivirus**: Primarily signature-based threat detection for malware on individual devices.

***

## Microsoft Questions (EN)

**1. When do you use Kerberos vs NTLM for authentication in RDP?**

* **Kerberos** is used when the machine is part of a domain and both the client and server are within a trusted domain.
* **NTLM** is used in cases where Kerberos isn’t possible (e.g., no domain trust, workgroup environments, or fallback when Kerberos fails).

**2. What is Global Catalog in Active Directory and how does it improve performance?** The **Global Catalog** stores a partial replica of all objects in the directory for fast object searches across domains. It reduces the load on domain controllers by enabling users to find objects without querying multiple domains.

**3. Describe a scenario for using forests, trees, and domains.** In a multinational organization with **different legal entities**, each could be set up as a separate **forest**. Within each forest, regions could be represented as **trees**, and the subdivisions within regions (e.g., departments) could be separate **domains**.

**4. What are the concepts of SSO, MFA, and OTP and why are they used?**

* **SSO (Single Sign-On)**: Allows users to log in once and access multiple systems, improving convenience.
* **MFA (Multi-Factor Authentication)**: Requires multiple forms of verification, improving security.
* **OTP (One-Time Password)**: Temporary password used for a single login, enhancing security.

**5. Why is Sysvol important in Active Directory?** **Sysvol** contains domain-wide files (e.g., group policy objects, logon scripts) that are replicated to all domain controllers, ensuring consistent policy application across the domain.

**6. What is the difference between domain local, global, and universal groups?**

* **Domain Local**: Used to assign permissions within the same domain.
* **Global**: Used to group users within the same domain and assign permissions in any domain.
* **Universal**: Used for grouping users from multiple domains to assign permissions across forests.

**7. What is a Pass-the-Hash attack and how do you mitigate it?** In **Pass-the-Hash**, attackers use stolen NTLM hash to authenticate without knowing the password. **Mitigation** includes:

* Enforcing strong authentication (e.g., MFA).
* Reducing NTLM use.
* Implementing **Privileged Access Workstations (PAWs)**.

**8. In which cases is NTLM still used in modern Active Directory and how can you minimize its use?** NTLM is used for backward compatibility, when Kerberos fails, or in workgroups. Minimize its use by:

* Enforcing **Kerberos**.
* Disabling NTLM where possible.

**9. What is the difference between NTLMv1 and NTLMv2?**

* **NTLMv1**: Older, less secure, vulnerable to certain attacks (e.g., rainbow tables).
* **NTLMv2**: Improved security with better encryption and mutual authentication.

**10. What is the role of an SPN in Kerberos and potential issues?** The **Service Principal Name (SPN)** uniquely identifies a service instance for Kerberos authentication. Incorrect SPN configuration can lead to authentication failures (e.g., **Kerberos double-hop issue**).

**11. Workgroup vs Domain differences?**

* **Workgroup**: Decentralized, each machine handles its own authentication.
* **Domain**: Centralized authentication via a domain controller.

**12. What is the role of RADIUS server in a Windows environment and how is it integrated with AD?** **RADIUS** provides centralized authentication for remote access (VPN, wireless). In a Windows environment, it integrates with Active Directory through **Network Policy Server (NPS)** to enforce access policies.
