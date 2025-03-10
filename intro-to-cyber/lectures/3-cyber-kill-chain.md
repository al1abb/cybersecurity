# 3 - Cyber Kill Chain

• A model, developed by Lockheed Martin, for cyber threat intelligence.&#x20;

• Not every attack by a black hat will follow a specific pattern of set of steps.&#x20;

• A kill chain is a series of steps an attacker must conduct to achieve an objective, it provides a structure to understand the abstract actions of an attack.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption><p>Cyber Kill Chain</p></figcaption></figure>

## 1. Reconnaissance

• **Main Goal**: Gathering information about the target&#x20;

• There are two different stages: **passive and active**&#x20;

– **Passive stage** uses indirect techniques to gather information without connecting with its systems&#x20;

– **Active stage** used direct techniques to gather information

### Passive Reconnaissance

• Mostly done by using **publicly available resources**&#x20;

– Open Source Intelligence (OSINT)

• Company websites and job listings&#x20;

• Advance web services usage such as: Shodan, Censys, DNS enumeration (WhoIs)

### Active Reconnaissance

• The attacker already has publicly available info

• **Direct interaction** by probing the network&#x20;

– Port scanning&#x20;

– Banner grabbing&#x20;

– Services enumeration&#x20;

– Vulnerability scanning

## 2. Weaponization

• **Main Goal**: Find or create the attack&#x20;

• Based on reconnaissance&#x20;

– Using popular frameworks like Metasploit&#x20;

– Publicly available exploit codes&#x20;

– Social Engineering toolkits&#x20;

– Anti-virus evasion frameworks&#x20;

– Crafting the attachments of a spearphishing email

## 3. Delivery

• **Main Goal**: Getting the exploit to the target systems

## 4. Exploitation

• **Main Goal**: Weapon-delivered attack executed

## 5. Installation

• **Main Goal**: Payload injection to gain better access&#x20;

• Maintain persistence&#x20;

• Survive reboots&#x20;

• Using offensive tools like RATs&#x20;

• PowerShell commands&#x20;

• Native workflow manipulation&#x20;

• Rootkits

## 6. Command & Control (C\&C)

• **Main Goal**: Remote control to weaponize the system&#x20;

• Used to deliver extra skills and abilities&#x20;

• Maintain wider infection and control&#x20;

• Execute the attack and gather information about the state in the victim’s environment

## 7. Action on Objective

• **Main Goal**: Attacker executes the desired action&#x20;

• Positive motivations for cyber attacks&#x20;

– Lateral movement&#x20;

– Exfiltration of data&#x20;

– Ransomware attacks

## How Black Hats find you?

• Public Available Sources&#x20;

• Misconfigured systems wide open on the internet&#x20;

• Can find these using **Shodan** – a tool that scans the internet for open services and systems&#x20;

• Lots of data on the internet to help craft an attack!

## Why we use Cyber Kill Chain

• Kill chains help to organize incident-response data in a way that allows you to visualize what the attack looked like and can help identify patterns in activity.&#x20;

• Real cybersecurity attacks can be simulated across all vectors to find vulnerabilities and threats based on this model. Simulation can help with identifying the different areas of risk.&#x20;

• Each stage of the kill chain requires specific tools to detect cyber attacks.&#x20;

• After identifying the risks, the next step is to fix the security gaps (This may include steps like installing patches and changing configurations).

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption><p>Cyber Security Pyramid of Pain</p></figcaption></figure>

## Associated Models

### Cyclic Representation

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption><p>Cyclic Representation</p></figcaption></figure>

### Mitre ATT\&CK Framework

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption><p>Cyber Kill Chain VS Mitre ATT&#x26;CK</p></figcaption></figure>

## Carbanak

Video about Carbanak Heist

{% embed url="https://www.youtube.com/watch?v=GSNopHdNnKE" %}
Carbanak Heist Documentary
{% endembed %}
