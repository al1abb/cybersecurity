---
description: 'Nice resource: https://www.thehacker.recipes/'
---

# FINAL INTERVIEW PREP

## AD

### NBT-NS vs DNS

* **NBT-NS** is primarily used for legacy support and local name resolution (via NetBIOS), whereas **DNS** is the modern and essential service used for global and domain name resolution in Active Directory.
* **NBT-NS** works with NetBIOS names and is limited to local networks, while **DNS** works with FQDNs and is essential for locating AD resources, including domain controllers.
* **NBT-NS** is still used when **DNS** cannot resolve the name or for compatibility with older systems and applications.
* In **Active Directory**, DNS is critical for the **domain controller location**, **Kerberos authentication**, and **replication**. NBT-NS is only used for backward compatibility and name resolution in older Windows environments.

In short, DNS is essential in Active Directory environments, whereas NBT-NS is more of a legacy protocol with limited use in modern AD deployments.

## LLMNR vs NBT-NS vs DNS

| Feature | **LLMNR** | **NBT-NS** | **DNS** |
| ------- | --------- | ---------- | ------- |

| **Purpose** | Resolves names on local network when DNS is unavailable | Resolves NetBIOS names to IP addresses | Resolves FQDNs to IP addresses, essential for AD |
| ----------- | ------------------------------------------------------- | -------------------------------------- | ------------------------------------------------ |

| **Scope** | Local network (link-local) | Local network (usually within the subnet) | Global (across internet and AD environments) |
| --------- | -------------------------- | ----------------------------------------- | -------------------------------------------- |

| **Protocol** | UDP port 5355 | UDP port 137 | UDP/TCP port 53 |
| ------------ | ------------- | ------------ | --------------- |

| **Type of Names Resolved** | Hostnames (e.g., `hostname.local`) | NetBIOS names (e.g., `WORKSTATION1`) | FQDNs (e.g., `server.example.com`) |
| -------------------------- | ---------------------------------- | ------------------------------------ | ---------------------------------- |

| **Uses in Active Directory** | Less commonly used, can be a fallback | Rarely used in modern AD environments | Critical for AD services (domain controllers, authentication) |
| ---------------------------- | ------------------------------------- | ------------------------------------- | ------------------------------------------------------------- |

| **Fallback Mechanism** | Yes, when DNS is not available | Yes, for legacy NetBIOS resolution | Primary and required in modern AD networks |
| ---------------------- | ------------------------------ | ---------------------------------- | ------------------------------------------ |

| **Security Considerations** | Vulnerable to MITM attacks, should be disabled if not needed | Vulnerable to name resolution poisoning and spoofing | Secure if configured correctly, supports DNSSEC |
| --------------------------- | ------------------------------------------------------------ | ---------------------------------------------------- | ----------------------------------------------- |

| **Modern Usage** | Used in smaller or simpler networks, can be disabled | Used in legacy systems or when DNS is not configured | Essential in all modern network infrastructures, especially AD |
| ---------------- | ---------------------------------------------------- | ---------------------------------------------------- | -------------------------------------------------------------- |



### Important ports in AD environment

* `53/TCP` and `53/UDP` for DNS
* `88/TCP` for Kerberos authentication
* `135/TCP` and `135/UDP` MS-RPC epmapper (EndPoint Mapper)
* `137/TCP` and `137/UDP` for NBT-NS
* `138/UDP` for NetBIOS datagram service
* `139/TCP` for NetBIOS session service
* `389/TCP` for LDAP
* `636/TCP` for LDAPS (LDAP over TLS/SSL)
* `445/TCP` and `445/UDP` for SMB
* `464/TCP` and `445/UDP` for Kerberos password change
* `3268/TCP` for LDAP Global Catalog
* `3269/TCP` for LDAP Global Catalog over TLS/SSL

### RID vs SID

| Feature               | **SID**                                                       | **RID**                                         |
| --------------------- | ------------------------------------------------------------- | ----------------------------------------------- |
| **Purpose**           | Globally unique identifier for security principals            | Unique identifier for objects within a domain   |
| **Structure**         | Domain identifier + RID                                       | The last part of the SID (individual object ID) |
| **Scope**             | Unique across the entire domain or forest                     | Unique within a single domain                   |
| **Use Case**          | Used in permissions, ACLs, authentication, and access control | Used to uniquely identify objects in a domain   |
| **Global Uniqueness** | Yes, it is globally unique in a forest                        | No, it is only unique within the domain         |

#### Summary:

* **SID** is a **global identifier** for an object in the Active Directory environment, used for security and access control across the entire domain or forest.
* **RID** is a **local identifier** within a domain, part of the SID, and uniquely identifies an object (like a user or group) within that domain. It is used to distinguish between objects in the same domain.

### DCSync

#### **What is DCSync?**

DCSync is a **post-exploitation technique** used in Active Directory (AD) environments to impersonate a Domain Controller (DC) and request sensitive data, including user password hashes. Essentially, it leverages the **Directory Replication Service (DRS)** protocol to pull data from the AD database.

Attackers use this technique to extract **password hashes**, **Kerberos keys**, and other critical secrets from the Domain Controller without needing direct access to it.

***

#### **How Does DCSync Work?**

1. **Active Directory Replication Basics:**
   * In AD, Domain Controllers replicate changes to each other using the **Directory Replication Service (DRS)**.
   * This service uses the **DSGetNCChanges()** function to synchronize data between DCs. It allows one DC to request information (e.g., password hashes) from another DC.
   * Only privileged accounts (e.g., Domain Admins, Enterprise Admins) or accounts with **replication permissions** can request this data.
2. **Abusing Replication Permissions:**
   * Attackers with **Domain Admin privileges** (or any account with replication rights) can abuse the DRS protocol to:
     * Pull **NTLM password hashes**.
     * Extract **Kerberos keys** (krbtgt account hash used to forge Golden Tickets).
     * Gather other sensitive user attributes.
   * This is done by simulating the behavior of a legitimate Domain Controller using tools like **Mimikatz**.
3. **Tools Used for DCSync:**
   * **Mimikatz**: Popular tool for executing DCSync. The `lsadump::dcsync` command allows attackers to fetch NTLM password hashes and Kerberos keys.
   * **Impacket**: Tools like `secretsdump.py` in Impacket can also perform DCSync attacks.

***

#### **Steps in a DCSync Attack:**

1. **Obtain Privileged Access:**
   * The attacker compromises an account with replication privileges (e.g., Domain Admin or an account explicitly delegated replication permissions).
   * Alternatively, escalate privileges to obtain such access.
2. **Execute the Attack:**
   * Use tools like Mimikatz or Impacket to simulate a Domain Controller and send replication requests.
   * The attacker requests specific data, such as:
     * NTLM password hashes for users.
     * The Kerberos **krbtgt** account hash.
     * Other sensitive AD attributes.
3.  **Utilize the Extracted Data:**

    * Use password hashes for **Pass-the-Hash (PtH)** attacks.
    * Forge **Golden Tickets** using the `krbtgt` hash for long-term AD persistence.
    * Move laterally and escalate privileges further in the network.



