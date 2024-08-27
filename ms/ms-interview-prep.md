# MS Interview Prep

## 1. What is Active Directory?

**Active Directory (AD)** is a **directory service** developed by Microsoft for Windows domain networks. It is a **centralized** and standardized system that automates network management of user data, security, and distributed resources, and enables interoperation with other directories.

AD stores information about objects on the network, such as users, computers, printers, and groups, and makes this information easily accessible to administrators and users.&#x20;

It uses a structured data store as the basis for a logical, hierarchical organization of directory information, such as a directory tree. This structure allows for efficient management of large amounts of data and resources across a network.

**Key features** of Active Directory include:

* **Authentication and Authorization**: AD provides secure login processes, verifying a user's identity and determining what resources they have access to.
* **Centralized Resource Management**: Administrators can manage resources and security settings across the network from a single point of control.
* **Group Policy**: AD allows for the configuration of settings for users and computers within the network using Group Policy Objects (GPOs).
* **Replication**: Data in AD is replicated across multiple domain controllers to ensure availability and fault tolerance.
* **Database**: AD relies on a database to store and organize directory information about the objects in the network, such as users, groups, computers, and security policies. \
  This database is often referred to as the "Active Directory database" and is stored in a file called **`NTDS.dit`** (C:\Windows\NTDS\NTDS.dit) on **each domain controller**.

Active Directory is essential in environments where centralized management and security are required, typically in business or enterprise settings.

## 2. How would you react to a colleague's criticism about your use of Active Directory?

I would approach this situation professionally. Now, I know that my AD skills might not be the best in a job environment as its not my specialty per say. Next, I would try to acknowledge their perspective as long as their feedback is valid. Then, I would try to explain my perspective on the subject and collaborate on a solution

## 3. What are the key changes in the 2012 version of Active Directory?

Active Directory in Windows Server 2012 introduced several enhancements over previous versions. Some key changes include:

* **AD Recycle Bin GUI:** This allows administrators to restore deleted objects directly from the GUI, simplifying the process.
* **Dynamic Access Control (DAC):** It provides a more granular approach to controlling access to files and folders by allowing claims-based access.
* **Virtualization-Safe AD:** Enhancements were made to make Active Directory more compatible with virtualized environments, including the ability to safely clone virtual domain controllers.
* **AD Administrative Center Improvements:** The ADAC was improved with new management features, such as the ability to manage Fine-Grained Password Policies.
* **PowerShell History Viewer:** This feature shows the PowerShell commands executed during an operation in ADAC, aiding in automation and scripting. These changes made AD more flexible, easier to manage, and more compatible with modern IT environments.

## 4. Describe tree, forest, domain, schema and Active Directory domain controller.

* **Tree:** In Active Directory, a tree is a collection of one or more domains that share a contiguous namespace. For example, if you have the domain `example.com`, a child domain under this could be `sales.example.com`.
* **Forest:** A forest is the top-level container in Active Directory that contains one or more trees. Trees in a forest share a common schema but do not necessarily share the same namespace.
* **Domain:** A domain is the fundamental organizational unit in AD. It is a collection of objects such as users, groups, and computers that share a common database and security policies. Domains are identified by their DNS name.
* **Schema:** The schema in Active Directory is a set of definitions that describe the kinds of objects and the types of information about those objects that can be stored in AD. It defines every object and attribute that the directory service uses.
* **Domain Controller (DC):** A domain controller is a server that responds to security authentication requests within the Windows Server domain. It stores a copy of the Active Directory database and is responsible for processing authentication requests, such as logging in to the domain.

## 5. Describe LDAP and Kerberos.

* **LDAP (Lightweight Directory Access Protocol):** LDAP is an open, vendor-neutral protocol used for accessing and maintaining distributed directory information services over an IP network. It is the protocol used to interact with Active Directory, allowing queries and updates to be made to the AD database.
* **Kerberos:** Kerberos is a network authentication protocol designed to provide strong authentication for client-server applications by using secret-key cryptography. It is the default authentication protocol in Windows environments, including Active Directory. Kerberos works by issuing tickets that allow users to authenticate to services without sending passwords over the network, thereby increasing security.

## 6. What is a PDC Emulator, and how can you determine if it works?

The PDC (Primary Domain Controller) Emulator is one of the five Flexible Single Master Operation (FSMO) roles in Active Directory. It is responsible for handling legacy compatibility with older systems, time synchronization within the domain, and acting as the authoritative source for password changes. To determine if the PDC Emulator is working, you can use the `netdom query fsmo` command or check through the Active Directory Users and Computers (ADUC) console under the Operations Masters. Additionally, ensuring the time synchronization across the domain is functioning properly can also indicate that the PDC Emulator is operational.

## 7. What's the difference between Authoritative and Non-Authoritative restore?

* **Authoritative Restore:** An authoritative restore is used when you need to recover deleted or changed objects in Active Directory and want the restored data to overwrite existing data across the entire domain. It marks the restored data as authoritative, so it replicates to all other domain controllers.
* **Non-Authoritative Restore:** This type of restore is typically used to recover a domain controller after a failure. The restored data is then updated through replication from other domain controllers, meaning it does not overwrite existing data unless the restored data is more recent.

## 8. Describe scenarios in which you can use Authoritative or Non-Authoritative restore and justify your choices.

* **Authoritative Restore Scenario:** Suppose a critical Organizational Unit (OU) containing multiple user accounts is accidentally deleted. An authoritative restore would be appropriate because it would allow you to recover the deleted OU and ensure it replicates across all domain controllers, restoring the directory to its previous state.
* **Non-Authoritative Restore Scenario:** Imagine a domain controller experiences hardware failure, and you need to restore its AD database from backup. A non-authoritative restore would be used because once the DC is restored, it will receive the latest changes from other DCs, ensuring it has the most up-to-date information without overwriting the current directory state.

## 9. What's the difference between Enterprise and Domain Admin groups?

* **Enterprise Admins:** This group has full control over all domains within the forest. Members can manage objects and settings across the entire AD forest, making it the most powerful group in a multi-domain environment.
* **Domain Admins:** This group has full control over a single domain. Members can manage all aspects of their respective domain, including creating users, groups, and other objects, but do not have inherent permissions in other domains within the forest.

## 10. Describe the relevance of KDC.

The Key Distribution Center (KDC) is a critical component of the Kerberos protocol, which is used for authentication in Active Directory environments. The KDC is responsible for issuing Kerberos tickets, which are used to authenticate users and services securely. In an AD domain, the KDC runs on every domain controller, ensuring that authentication is handled within the domain’s security boundaries.

## 11. List a few ports in Active Directory.

Some commonly used ports in Active Directory include:

* **LDAP:** Port 389 (TCP/UDP) for standard communication.
* **LDAPS:** Port 636 (TCP) for secure LDAP communication.
* **Kerberos:** Port 88 (TCP/UDP) for authentication.
* **Global Catalog:** Port 3268 (TCP) for non-SSL and 3269 (TCP) for SSL.
* **DNS:** Port 53 (TCP/UDP) for name resolution.

## 12. Explain what the `gpupdate /force` command does.

The `gpupdate /force` command in Windows is used to manually refresh Group Policy settings on a computer. By using the `/force` switch, the command forces the reapplication of all policy settings, regardless of whether they have changed or not. This is particularly useful when you’ve made changes to Group Policies and want them to be applied immediately across the affected systems.

## 13. What's the SYSVOL folder, and why is it important?

The SYSVOL folder is a shared directory that stores the server copy of the domain’s public files, which are necessary for Active Directory, such as Group Policy objects and scripts that need to be accessed by users or computers in the domain. It is replicated across all domain controllers in the domain, ensuring consistency. The integrity and availability of SYSVOL are crucial for the proper functioning of Group Policies and login scripts.

## 14. Define the infrastructure master.

The Infrastructure Master is one of the five FSMO roles in Active Directory. It is responsible for updating cross-domain group memberships. It handles references to objects in other domains and ensures that these references are correctly maintained when objects are moved or renamed in other domains. The Infrastructure Master should not be hosted on a domain controller that is also a Global Catalog server unless all DCs in the domain are Global Catalog servers.

## 15. Give an example of a namespace.

A namespace is a logical grouping of objects in Active Directory. An example of a namespace could be `example.com`, where `example.com` is the root domain, and it can contain child domains like `sales.example.com` or `marketing.example.com`. The namespace helps in organizing the domain structure and provides a way to locate objects within the AD hierarchy.

## 16. What does RODC stand for?

RODC stands for Read-Only Domain Controller. It is a type of domain controller introduced in Windows Server 2008 that hosts a read-only copy of the Active Directory database. RODCs are typically deployed in locations where physical security cannot be guaranteed, as they do not allow changes to be made directly on them, which enhances security by preventing unauthorized modifications to the Active Directory. Any changes made at an RODC are referred back to a writable domain controller for processing.

## 17. Which file can a user view to identify SRV records associated with a domain controller?

A user can view the `netlogon.dns` file to identify SRV (Service) records associated with a domain controller. This file is typically located in the `C:\Windows\System32\config` folder on a domain controller. It contains the DNS records that are registered by the domain controller in DNS, including the SRV records that indicate the services the domain controller provides.

## 18. Explain the role of subnets in your work.

Subnets in Active Directory play a crucial role in defining the physical topology of the network. They are used to associate IP addresses with specific sites in AD, which helps in optimizing authentication and replication traffic. By correctly defining subnets and associating them with sites, you ensure that clients authenticate with the closest domain controller, thereby reducing network latency and improving performance. Subnets also help in managing site-specific Group Policies and replication schedules.

## 19. Do you have any experience with unidirectional trust and bi-directional trust?

Yes, in Active Directory, a trust relationship defines the authentication paths between two domains or forests.

* **Unidirectional Trust:** This type of trust allows access only from the trusted domain to the trusting domain. For example, Domain A trusts Domain B, but Domain B does not trust Domain A.
* **Bi-directional Trust:** In this trust, both domains trust each other, allowing users from both domains to access resources in either domain. For example, Domain A and Domain B trust each other mutually.

Trusts are essential in scenarios where different domains or forests need to share resources securely, and the choice between unidirectional and bi-directional trust depends on the level of access required between the domains.

## 20. Discuss the importance of multiple-master replication to your work.

Multiple-master replication in Active Directory means that changes can be made on any domain controller, and those changes will be replicated to all other domain controllers in the domain. This ensures high availability and redundancy, as no single domain controller is a point of failure. In my work, this is crucial because it allows flexibility in making changes, such as updating user attributes or creating objects, without waiting for a specific DC to be available. It also helps maintain consistency across the network, as all domain controllers eventually converge to the same state through replication.

## 21. Tell me about a scenario in which generic containers are relevant.

Generic containers in Active Directory are used to hold objects that do not fit into the standard categories like users, groups, or computers. For instance, when dealing with service accounts or application-specific configurations that do not need to be placed in a specific OU (Organizational Unit), generic containers like `CN=Users` or `CN=Computers` can be used. These containers are especially relevant when integrating third-party applications that require storing metadata or configuration settings within AD.

## 22. When can you use an application partition?

An application partition in Active Directory is a directory partition that is replicated only to specific domain controllers. It is used for storing application-specific data that does not need to be replicated to every DC in the forest. For example, DNS zone data in an AD-integrated DNS setup can be stored in an application partition, ensuring that only domain controllers that also serve as DNS servers hold a copy of this data. This approach reduces replication traffic and improves performance by limiting replication to only the necessary domain controllers.

## 23. How do you check the tombstone lifetime value in your forest?

To check the tombstone lifetime value in an Active Directory forest, you can use the ADSI Edit tool. Connect to the Configuration partition, navigate to `CN=Directory Service, CN=Windows NT, CN=Services, CN=Configuration, DC=ForestRootDomain`, and check the `tombstoneLifetime` attribute. The tombstone lifetime defines how long deleted objects are retained in AD before they are permanently removed, which is crucial for managing replication and recovery processes.

## 24. Describe the process for configuring Universal Group Membership Caching.

Universal Group Membership Caching (UGMC) is a feature that allows a domain controller to cache the universal group memberships of users, reducing the need to contact a Global Catalog server for authentication. To configure UGMC:

1. Open the Active Directory Sites and Services console.
2. Expand the `Sites` container and select the site where you want to enable caching.
3. Right-click on the `NTDS Site Settings` under the site, and select `Properties`.
4. Check the `Enable Universal Group Membership Caching` box.
5. Optionally, you can specify a replication partner to refresh the cache periodically. This feature is particularly useful in branch office scenarios where network connectivity to a Global Catalog server may be unreliable or slow.

## 25. Can you provide examples of windows security protected files

Examples include `ntoskrnl.exe`, `lsass.exe`, and the `SAM` file.

Windows security-protected files include critical system files that are protected by Windows File Protection (WFP) or Windows Resource Protection (WRP). Examples include:

* **`C:\Windows\System32\ntdll.dll`:** A key system file involved in the operation of Windows.
* **`C:\Windows\System32\kernel32.dll`:** Handles memory management, input/output operations, and interrupts.
* **`C:\Windows\System32\hal.dll`:** The Hardware Abstraction Layer, essential for Windows to communicate with the hardware. These files are protected to prevent unauthorized modifications, which could compromise system stability and security.

## 26. Explain what a schema is as if you were talking to a client with little IT knowledge.

A schema in Active Directory is like a blueprint or a set of instructions that defines all the types of information that can be stored in the directory. Imagine you have a big filing cabinet, and the schema tells you what kinds of files (like forms) you can put in it, how those forms should be filled out, and what information they should contain. For example, it defines what a “user” is, what information about the user should be stored (like their name, email, and password), and how that information should be organized. It helps ensure that all the information in the directory is consistent and follows the same rules.

## 27. What is a PDC emulator?

The PDC (Primary Domain Controller) Emulator is one of the five Flexible Single Master Operation (FSMO) roles in Active Directory. It acts as a bridge for backward compatibility with older Windows NT systems, serves as the primary source for time synchronization within the domain, and is the authoritative source for password changes. The PDC Emulator also handles account lockouts and serves as the domain’s source for GPO (Group Policy Object) changes. To verify its operation, you can use tools like `netdom query fsmo` or check its status via the Active Directory Users and Computers (ADUC) console.

## 28. List common RDN prefixes.

RDN (Relative Distinguished Name) prefixes are used to uniquely identify objects within a container in Active Directory. Common RDN prefixes include:

* **`CN` (Common Name):** Used for users, computers, and groups (e.g., `CN=John Doe`).
* **`OU` (Organizational Unit):** Used for organizational units (e.g., `OU=Sales`).
* **`DC` (Domain Component):** Represents components of the domain name (e.g., `DC=example, DC=com`).
* **`CN` for containers:** Used for generic containers like `CN=Users` or `CN=Computers`. These prefixes help in structuring and identifying objects within the AD hierarchy.

## 29. What do WEB I and DSS stand for?

* **WEB I:** This acronym typically refers to web-based interfaces or integrated web environments, but it is not a standard term in Active Directory. In some contexts, it may refer to web integration or interfaces related to directory services.
* **DSS (Directory Synchronization Service):** DSS refers to a server that provides directory services, such as Active Directory Domain Services (AD DS) in a Windows environment. It is responsible for storing and managing directory information and facilitating authentication and directory-related operations.

## 30. Describe the purpose of the Active Directory Recycle Bin.

The Active Directory Recycle Bin is a feature that allows administrators to restore deleted objects in AD without having to restore from a backup or perform an authoritative restore. Introduced in Windows Server 2008 R2, the Recycle Bin preserves the attributes of deleted objects, making recovery quick and straightforward. When an object is deleted, it is moved to the Recycle Bin instead of being permanently removed. Administrators can then restore the object through the AD Administrative Center or using PowerShell, fully recovering its attributes and links to other objects. This feature is crucial for minimizing downtime and data loss due to accidental deletions.

## 31. Explain why a database administrator might use replication.

In the context of Active Directory, replication ensures that changes made to the directory database on one domain controller are copied to all other domain controllers in the domain or forest. A database administrator would use replication to ensure consistency and availability of data across multiple locations. This redundancy helps maintain the integrity of the directory, as it allows for automatic failover in case one domain controller becomes unavailable. Replication also helps in load balancing, distributing authentication and query requests across multiple servers to improve performance.

## 32. What's the difference between native mode and mixed mode?

* **Native Mode:** This mode in Active Directory allows all domain controllers to run on Windows 2000 or later versions. It supports advanced features such as Universal Groups, Group Nesting, and SID History. In native mode, only Active Directory domain controllers can be part of the domain.
* **Mixed Mode:** In mixed mode, the domain can have a mix of Windows NT 4.0 domain controllers and newer versions (Windows 2000 or later). However, it limits some advanced features to maintain compatibility with the older NT domain controllers. Mixed mode is typically used during a migration phase, and the goal is usually to move to native mode once all domain controllers have been upgraded.

## 33. Is clustering relevant in Active Directory?

Clustering is not typically used with Active Directory Domain Services itself, as AD relies on its built-in replication and multiple-master model for high availability. However, clustering can be relevant for applications that depend on AD, such as SQL Server or Exchange, where you might use Windows Server Failover Clustering (WSFC) to ensure high availability and redundancy. In these cases, clustering helps minimize downtime for critical services that rely on Active Directory for authentication and directory services.

## 34. Tell me about the similarities and differences between Active Directory's physical and logical structures.

* **Physical Structure:** The physical structure of Active Directory refers to the physical components, like domain controllers, sites, and site links, that define the network topology. Sites represent the physical grouping of IP subnets, and site links define the network paths for replication between these sites.
* **Logical Structure:** The logical structure includes domains, organizational units (OUs), trees, and forests. These elements represent the organization of objects within the directory based on business needs rather than physical location. The key similarity is that both structures are essential for the proper functioning of AD and help in managing the directory efficiently. The physical structure optimizes replication and network traffic, while the logical structure provides a way to organize resources according to business requirements.

## 35. How can you address lingering objects?

Lingering objects occur when a domain controller is disconnected from the network for a period longer than the tombstone lifetime, and then reconnected without being properly cleaned up. These objects can cause inconsistencies and replication errors. To address lingering objects:

1. **Identify the Lingering Objects:** Use the `repadmin /removelingeringobjects` command to detect and remove lingering objects from a domain controller.
2. **Clean Up the DC:** If necessary, force a replication from a clean source using the `repadmin /syncall` command.
3. **Prevent Future Issues:** Regularly monitor replication and ensure that domain controllers are not offline for extended periods. Adjust the tombstone lifetime if necessary to match your organization's replication needs.

## 36. Why is it important to assign unique IDs to an object?

In Active Directory, each object is assigned a unique identifier called a Security Identifier (SID). This unique ID is critical because it ensures that each object can be distinctly recognized and managed within the directory, even if objects have the same name. SIDs are used in access control lists (ACLs) to manage permissions and security, ensuring that only the correct users or groups have access to specific resources. Unique IDs also prevent conflicts and maintain consistency across the directory during replication and other operations.

## 37. How does the Kerberos authentication process works?

Kerberos is a secure authentication protocol that uses a system of tickets to authenticate users and services in a network. The process involves three main components: the client, the Key Distribution Center (KDC), and the service.

1. **Initial Authentication:** The user enters their credentials, which are sent to the KDC. The KDC verifies the credentials and issues a Ticket Granting Ticket (TGT).
2. **Service Request:** When the user wants to access a service, they present the TGT to the KDC and request a service ticket.
3. **Service Access:** The KDC issues a service ticket, which the user presents to the target service. The service then verifies the ticket and grants access. This process ensures that passwords are never sent over the network and that authentication is handled securely through the use of encrypted tickets.

## 38. What is Prefetch Folder in Windows

The Prefetch folder in Windows is part of the system's performance optimization mechanisms. It stores prefetch files, which are used to speed up the startup process of applications. When you launch an application, Windows creates a prefetch file containing information about the files that are loaded during startup. This file helps Windows to optimize the loading process by preloading these files into memory the next time the application is started. The Prefetch folder is located in `C:\Windows\Prefetch`.

## 39. What does the Export-VM command do?

The `Export-VM` command in Windows PowerShell is used to export a virtual machine (VM) and its configuration files, including its virtual hard disks (VHDs), to a specified location. This command is often used for backing up VMs or for moving them to another host. The export includes the entire state of the VM, making it possible to import it later using the `Import-VM` command. This functionality is crucial for disaster recovery and for migrating VMs between hosts.

## 40. What are Federation Services and Certificate Services in an Active Directory environment

* **Active Directory Federation Services (AD FS):** AD FS provides Single Sign-On (SSO) capabilities, allowing users to access multiple applications across different organizations using a single set of credentials. It enables secure sharing of identity information between trusted partners (federation) and is commonly used in scenarios where an organization wants to provide access to external partners or cloud services without requiring separate logins.
* **Active Directory Certificate Services (AD CS):** AD CS is used to create, manage, and distribute digital certificates within an organization. These certificates can be used for encrypting data, securing communications, and authenticating users and devices. AD CS is essential for implementing Public Key Infrastructure (PKI) in an organization, ensuring that communications and transactions are secure.
