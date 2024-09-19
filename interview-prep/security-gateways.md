# Security Gateways

## 1. CIA nədir?

**CIA (Confidentiality, Integrity, Availability)** - The CIA triad is a model used to guide policies for information security.

* **Confidentiality** - Ensures that information is accessible only to those authorized to have access.
* **Integrity** - Ensures that information is accurate and has not been altered by unauthorized individuals.
* **Availability** - Ensures that information and resources are available to authorized users when needed.

## 2. Firewall nədir?

**Firewall** - A network security device or software that monitors and controls incoming and outgoing network traffic based on predetermined security rules. Its primary function is to establish a barrier between a trusted internal network and untrusted external networks.

## 3. İmplicit Deny nədir?

**Implicit Deny** - A security principle where access to a resource is denied by default unless explicitly allowed. This means that unless a rule specifically allows access, it is automatically denied.

## 4. Firewall konsepti

**Firewall Concept** - The concept involves using firewalls to filter network traffic based on rules that define what traffic is allowed or blocked. Firewalls can be implemented in hardware, software, or a combination of both and are used to protect networks from unauthorized access and attacks.

## 5. Firewall Interfaces

**Firewall Interfaces** - Firewalls typically have multiple interfaces:

* **Internal Interface** - Connected to the internal, trusted network.
* **External Interface** - Connected to the external, untrusted network (e.g., the internet).
* **DMZ Interface** - Connected to a demilitarized zone (DMZ) where public-facing services reside.

## 6. Proxy server

**Proxy Server** - An intermediary server that separates end users from the websites they browse. It can be used for various purposes, such as improving security, filtering content, and caching data to improve performance.

## 7. Stateless Firewalls

**Stateless Firewalls** - Firewalls that filter traffic based solely on individual packets without considering the context of the traffic (e.g., the state of a connection). They use predefined rules to allow or block traffic but do not keep track of the state of active connections.

## 8. Statefull Firewalls

**Stateful Firewalls** - Firewalls that monitor the state of active connections and make decisions based on the state and context of the traffic. They can maintain a state table to track ongoing connections and provide more advanced filtering than stateless firewalls.

## 9. NGFW (Next Generation Firewall)

**NGFW** - A more advanced type of firewall that includes traditional firewall functionalities along with additional features such as deep packet inspection, application awareness, intrusion prevention systems (IPS), and threat intelligence.

## 10. UTM (Unified Threat Management)

**UTM** - A comprehensive security solution that integrates multiple security features, such as firewall, antivirus, antispam, VPN, and intrusion detection/prevention systems into a single device or service.

## 11. IDS

**IDS** - A system that monitors network or system activities for malicious activities or policy violations. It alerts administrators when such activities are detected but does not take direct action to block them.

## 12. IPS

**IPS** - A system that not only detects and alerts on malicious activities but also actively takes measures to block or prevent them from causing harm to the network or system.

## 13. Antivirus

**Antivirus** - Software designed to detect, prevent, and remove malicious software (malware) from computer systems. It scans files and programs for known malware signatures and behavior.

## 14. VPN

**VPN** - A technology that creates a secure, encrypted connection over a less secure network, such as the internet. It is used to protect data in transit and to provide remote access to internal network resources.

## 15. DLP (Data Loss Prevention)

**DLP** - A set of strategies and tools used to prevent unauthorized access, leakage, or loss of sensitive data. It involves monitoring and controlling data flows and usage to ensure that sensitive information remains protected.

## 16. Zero Trust Model

**Zero Trust Model** - A security framework that assumes that threats could be both external and internal, and therefore no user or system should be trusted by default. Verification is required from everyone attempting to access resources, regardless of their location.

## 17. NAT - Network Address Translation (PAT)

**NAT (Network Address Translation)** - A technique used to modify IP address information in packet headers while in transit across a router or firewall. **PAT (Port Address Translation)** is a type of NAT that allows multiple devices on a local network to share a single public IP address.

## 18. Virtual IP (VIP) nədir?

**Virtual IP (VIP)** - An IP address that is not tied to a specific physical network interface but is used to provide redundancy and load balancing by mapping to multiple physical addresses.

## 19. AAA (Authentication, Authorization, and Accounting)

**AAA** - A framework for managing user access and tracking activity.

* **Authentication** - Verifies the identity of users.
* **Authorization** - Determines what resources a user can access.
* **Accounting** - Tracks user activities and resource usage.

## 20. RADİUS, TACACS+

* **RADIUS (Remote Authentication Dial-In User Service)** - A protocol for network access authentication, authorization, and accounting, typically used for remote access.
* **TACACS+ (Terminal Access Controller Access-Control System Plus)** - A protocol for centralized authentication, authorization, and accounting, offering more detailed control compared to RADIUS.

## 21. VDOM

**VDOM** - A feature in some firewalls and network devices that allows the creation of multiple virtual instances (domains) on a single physical device, enabling separate, isolated environments for different purposes.

## 22. DOS/ DDOS attack

* **DoS (Denial of Service) Attack** - An attack designed to disrupt the normal functioning of a service or network by overwhelming it with a flood of illegitimate requests.
* **DDoS (Distributed Denial of Service) Attack** - A DoS attack that uses multiple compromised systems to flood a target with traffic, making it more difficult to mitigate.

## 23. SYN flood attack

**SYN Flood Attack** - A type of DoS attack that exploits the TCP handshake process by sending a flood of SYN requests to a target, consuming server resources and preventing legitimate connections.

## 24. DHCP starving

**DHCP Starving** - An attack where an attacker exhausts the DHCP server's IP address pool by requesting all available IP addresses, preventing legitimate clients from obtaining IP addresses.

## 25. SSL/TLS

**SSL (Secure Sockets Layer)** and **TLS (Transport Layer Security)** - Protocols for establishing a secure, encrypted connection between a client and server over a network. TLS is the successor to SSL and is more secure.
