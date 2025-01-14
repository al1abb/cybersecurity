# Footprinting

## Enumeration

{% hint style="info" %}
Our goal is not to get at the systems but to find all the ways to get there.
{% endhint %}

Principles

<table data-header-hidden><thead><tr><th width="98"></th><th></th></tr></thead><tbody><tr><td><strong><code>No.</code></strong></td><td><strong><code>Principle</code></strong></td></tr><tr><td>1.</td><td>There is more than meets the eye. Consider all points of view.</td></tr><tr><td>2.</td><td>Distinguish between what we see and what we do not see.</td></tr><tr><td>3.</td><td>There are always ways to gain more information. Understand the target.</td></tr></tbody></table>

The whole enumeration process is divided into three different levels:

| `Infrastructure-based enumeration` | `Host-based enumeration` | `OS-based enumeration` |
| ---------------------------------- | ------------------------ | ---------------------- |

<figure><img src="../../.gitbook/assets/image (59).png" alt=""><figcaption></figcaption></figure>

Consider these lines as some kind of obstacle, like a wall, for example. What we do here is look around to find out where the entrance is, or the gap we can fit through, or climb over to get closer to our goal. Theoretically, it is also possible to go through the wall headfirst, but very often, it happens that the spot we have smashed the gap with a lot of effort and time with force does not bring us much because there is no entry at this point of the wall to pass on to the next wall.

## Enumeration Layers

<table data-header-hidden><thead><tr><th width="199"></th><th></th><th></th></tr></thead><tbody><tr><td><strong>Layer</strong></td><td><strong>Description</strong></td><td><strong>Information Categories</strong></td></tr><tr><td><code>1. Internet Presence</code></td><td>Identification of internet presence and externally accessible infrastructure.</td><td>Domains, Subdomains, vHosts, ASN, Netblocks, IP Addresses, Cloud Instances, Security Measures</td></tr><tr><td><code>2. Gateway</code></td><td>Identify the possible security measures to protect the company's external and internal infrastructure.</td><td>Firewalls, DMZ, IPS/IDS, EDR, Proxies, NAC, Network Segmentation, VPN, Cloudflare</td></tr><tr><td><code>3. Accessible Services</code></td><td>Identify accessible interfaces and services that are hosted externally or internally.</td><td>Service Type, Functionality, Configuration, Port, Version, Interface</td></tr><tr><td><code>4. Processes</code></td><td>Identify the internal processes, sources, and destinations associated with the services.</td><td>PID, Processed Data, Tasks, Source, Destination</td></tr><tr><td><code>5. Privileges</code></td><td>Identification of the internal permissions and privileges to the accessible services.</td><td>Groups, Users, Permissions, Restrictions, Environment</td></tr><tr><td><code>6. OS Setup</code></td><td>Identification of the internal components and systems setup.</td><td>OS Type, Patch Level, Network config, OS Environment, Configuration files, sensitive private files</td></tr></tbody></table>

{% hint style="info" %}
Important note: The human aspect and the information that can be obtained by employees using OSINT have been removed from the "Internet Presence" layer for simplicity.
{% endhint %}

We can finally imagine the entire penetration test in the form of a labyrinth where we have to identify the gaps and find the way to get us inside as quickly and effectively as possible. This type of labyrinth may look something like this: (The squares represent the gaps/vulnerabilities.)

<figure><img src="../../.gitbook/assets/image (60).png" alt=""><figcaption></figcaption></figure>

Let us assume that we have been asked to perform an external "black box" penetration test. Once all the necessary contract items have been completely fulfilled, our penetration test will begin at the specified time.

### Layer No.1: Internet Presence

The first layer we have to pass is the "Internet Presence" layer, where we focus on finding the targets we can investigate. If the scope in the contract allows us to look for additional hosts, this layer is even more critical than for fixed targets only. In this layer, we use different techniques to find domains, subdomains, netblocks, and many other components and information that present the presence of the company and its infrastructure on the Internet.

`The goal of this layer is to identify all possible target systems and interfaces that can be tested.`

### Layer No.2: Gateway

`The goal is to understand what we are dealing with and what we have to watch out for`

### Layer No.3: Accessible Services

In the case of accessible services, we examine each destination for all the services it offers. Each of these services has a specific purpose that has been installed for a particular reason by the administrator. Each service has certain functions, which therefore also lead to specific results. To work effectively with them, we need to know how they work. Otherwise, we need to learn to understand them.

`This layer aims to understand the reason and functionality of the target system and gain the necessary knowledge to communicate with it and exploit it for our purposes effectively.`

### Layer No.4: Processes

Every time a command or function is executed, data is processed, whether entered by the user or generated by the system. This starts a process that has to perform specific tasks, and such tasks have at least one source and one target.

`The goal here is to understand these factors and identify the dependencies between them.`

### Layer No.5: Privileges

Each service runs through a specific user in a particular group with permissions and privileges defined by the administrator or the system. These privileges often provide us with functions that administrators overlook. This often happens in Active Directory infrastructures and many other case-specific administration environments and servers where users are responsible for multiple administration areas.

`It is crucial to identify these and understand what is and is not possible with these privileges.`

### Layer No.6: OS Setup

Here we collect information about the actual operating system and its setup using internal access. This gives us a good overview of the internal security of the systems and reflects the skills and capabilities of the company's administrative teams.

`The goal here is to see how the administrators manage the systems and what sensitive internal information we can glean from them.`

