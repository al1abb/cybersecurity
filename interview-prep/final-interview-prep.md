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



