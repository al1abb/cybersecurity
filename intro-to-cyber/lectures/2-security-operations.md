# 2 - Security Operations

## Assets

• One of the main tasks of risk professionals is providing secure systems to protect the main assets of the organization&#x20;

• An asset is anything that can be used to produce value for your organization.”&#x20;

• What we are trying to protect - people, property and information.&#x20;

– It must have value to you, your oorganization or a country, otherwise there is no need to protect it.

### Examples of Assets

• Data (customer data, databases, patents, marketing information on clients)&#x20;

• Physical assets (devices, machines, servers, hardware, software)&#x20;

• Processes (especially in OT situations) OT = Operational Technology

• People / Personnel&#x20;

• Reputation, goodwill

### Tangible and Intangible assets

**Tangible assets** - are physical; they include cash, inventory, vehicles, equipment, buildings or investment

**Intangible assets** - do not exist in physical form and include things like IP addresses, data, etc.

### Asset Criticality

• Nobody has unlimited resources, therefore there is a need to prioritize security efforts according to the asset’s criticality to the business&#x20;

• Assessing risks should focus on those assets deemed to be most critical&#x20;

• Criticality is typically defined as a measure of the consequences associated with the loss or degradation of a particular asset&#x20;

• In other words, what is there to lose?

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption><p>Asset Criticality</p></figcaption></figure>

## CIA Triad

### Confidentiality Controls

• Measures undertaken to ensure confidentiality are designed to prevent sensitive information from reaching the wrong people while ensuring that the right people can get it.&#x20;

• Ensuring that information is accessible only to those authorized to have access. Normally achieved using:&#x20;

• Identity management&#x20;

– Authorization of access&#x20;

• Encryption&#x20;

– Securing information at rest, in transit, and in use&#x20;

• The use of digital signatures

### Integrity Controls

• Both data stored on systems and transmitted (Ex. e-mail)&#x20;

• Control access (Authentication and Authorization)&#x20;

• Hashing and data checksums&#x20;

• Physical assets protection&#x20;

• Blacklisting and whitelisting&#x20;

• Software: OSSEC, SEM, Tripwire, Qualys FIM, Trustwave EP&#x20;

– Integrity monitors, detect unauthorized modifications, etc.

### Availability Controls

• Maintaining all hardware, perform hardware repairs immediately when needed&#x20;

• Maintaining a properly functioning operation system (OS), perform system hardening&#x20;

• Redundancy (having extra or duplicate resources available to support the main system) / Backups and RAID&#x20;

• Failover (The process of switching to a functional asset when failure termination happened)&#x20;

• Disaster recovery / COOP plan

## Controls Classification

• **Physical controls** - Fences, doors, locks and fire extinguishers;&#x20;

• **Administrative controls** (Human factors of security)

– Legal and regulatory or compliance controls - privacy laws, policies and clauses.&#x20;

– Procedural controls - Incident response processes, management oversight, security awareness and training.&#x20;

• **Technical controls** - User authentication (login) and logical access controls, antivirus software, firewalls.

## Defense In Depth

• Defense in depth is an information assurance (IA) concept in which multiple layers of security controls (defenses) are placed throughout an information technology (IT) system.&#x20;

• **Every layer must use different strategies and mitigations.**

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>
