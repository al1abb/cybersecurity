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

Key changes include the Active Directory Recycle Bin, a simplified deployment, and dynamic access control, which improves security by allowing fine-grained access permissions.

## 4. Describe tree, forest, domain, schema and Active Directory domain controller.

* **Tree**: A collection of one or more domains sharing a common namespace.
* **Forest**: A collection of one or more trees that share a global catalog.
* **Domain**: A logical group of network objects, like users and computers, within a tree.
* **Schema**: The structure that defines all objects and their attributes in AD.
* **Domain Controller (DC)**: A server that manages domain resources, authenticates users, and enforces policies.

## 5. Describe LDAP and Kerberos.

* **LDAP**: A protocol for accessing and maintaining distributed directory information like user details.
* **Kerberos**: A network authentication protocol that uses tickets to allow secure user authentication.

## 6. What is a PDC Emulator, and how can you determine if it works?

The Primary Domain Controller (PDC) Emulator synchronizes time and manages password changes. You can check its status using the `netdom query fsmo` command.

## 7. What's the difference between Authoritative and Non-Authoritative restore?

* **Authoritative Restore**: Recovers specific AD objects and marks them as the latest, so they overwrite others.
* **Non-Authoritative Restore**: Restores AD data to a previous state but allows it to be updated by other domain controllers.

## 8. Describe scenarios in which you can use Authoritative or Non-Authoritative restore and justify your choices.

* **Authoritative**: Use when an important object (e.g., user account) is accidentally deleted.
* **Non-Authoritative**: Use when recovering AD after a server crash; this allows the latest changes from other servers to be replicated.

## 9. What's the difference between Enterprise and Domain Admin groups?

* **Enterprise Admins**: Have full control across the entire AD forest.
* **Domain Admins**: Have control within a specific domain.

## 10. Describe the relevance of KDC.

The Key Distribution Center (KDC) in AD is crucial for Kerberos authentication, issuing tickets to verify user identities.

## 11. List a few ports in Active Directory.

Common ports include:

* **LDAP**: 389 (TCP/UDP)
* **LDAPS**: 636 (TCP)
* **Kerberos**: 88 (TCP/UDP)
* **Global Catalog**: 3268 (TCP)

## 12. Explain what the `gpupdate /force` command does.

This command forces an immediate update of Group Policy settings on a computer, overriding the normal refresh interval.

## 13. What's the SYSVOL folder, and why is it important?

SYSVOL is a shared directory that stores important AD files like group policies and scripts. It's critical for consistent policy application across the domain.

## 14. Define the infrastructure master.

The Infrastructure Master updates references to objects in other domains and ensures consistency of object references across domains.

## 15. Give an example of a namespace.

A namespace could be something like `company.local`, which organizes resources in AD and DNS.

## 16. What does RODC stand for?

Read-Only Domain Controller. It provides a way to deploy domain controllers in locations with lower security by preventing changes to AD.

## 17. Which file can a user view to identify SRV records associated with a domain controller?

The `netlogon.dns` file in the `C:\Windows\System32\config` folder lists SRV records for domain controllers.

## 18. Explain the role of subnets in your work.

Subnets help to organize and optimize network traffic by defining which IP addresses are within certain boundaries, critical for AD site design and replication.

## 19. Do you have any experience with unidirectional trust and bi-directional trust?

Unidirectional trust allows one domain to trust another, while bi-directional trust allows both domains to trust each other. Experience in setting them up is useful for cross-domain authentication.

## 20. Discuss the importance of multiple-master replication to your work.

Multiple-master replication allows any domain controller to accept changes and replicate them to others, ensuring high availability and fault tolerance.

## 21. Tell me about a scenario in which generic containers are relevant.

Generic containers, like `Users` and `Computers`, are used to organize objects in AD for easier management and policy application.

## 22. When can you use an application partition?

Application partitions are used to replicate data to specific domain controllers rather than the entire forest, useful for data like DNS information.

## 23. How do you check the tombstone lifetime value in your forest?

You can check it using the `dsquery * “cn=Directory Service, cn=Windows NT, cn=Services, cn=Configuration, dc=domain, dc=com” –scope base –attr tombstonelifetime` command.

## 24. Describe the process for configuring Universal Group Membership Caching.

Enable it in the AD Sites and Services console to allow users to log on even when the Global Catalog server is unavailable.

## 25. Can you provide examples of windows security protected files

Examples include `ntoskrnl.exe`, `lsass.exe`, and the `SAM` file.

## 26. Explain what a schema is as if you were talking to a client with little IT knowledge.

The schema is like a blueprint for your network directory; it defines what types of information (like user details) can be stored and how it's organized.

## 27. What is a PDC emulator?

The PDC Emulator is a role in AD that ensures consistent time across the network and manages password changes and account lockouts.

## 28. List common RDN prefixes.

* `CN` (Common Name)
* `OU` (Organizational Unit)
* `DC` (Domain Component)

## 29. What do WEB I and DSS stand for?

* **WEB I**: No specific common acronym, might need clarification.
* **DSS**: Decision Support System, typically used for making informed decisions based on data.

## 30. Describe the purpose of the Active Directory Recycle Bin.

The AD Recycle Bin allows you to restore accidentally deleted objects like users or groups without needing a full restore.

## 31. Explain why a database administrator might use replication.

Replication ensures that changes made to the database are copied to other servers, providing data redundancy and high availability.

## 32. What's the difference between native mode and mixed mode?

* **Native Mode**: Only supports Windows 2000 or later domain controllers.
* **Mixed Mode**: Supports both Windows 2000 and NT domain controllers.

## 33. Is clustering relevant in Active Directory?

Yes, clustering can be used for high availability of certain AD services, like databases, though not directly for domain controllers.

## 34. Tell me about the similarities and differences between Active Directory's physical and logical structures.

* **Physical Structure**: Refers to the actual network layout, like sites and domain controllers.
* **Logical Structure**: Refers to how AD objects like domains and OUs are organized and managed.

## 35. How can you address lingering objects?

Lingering objects can be removed using tools like `repadmin` to ensure consistency across domain controllers.

## 36. Why is it important to assign unique IDs to an object?

Unique IDs, like SIDs, ensure that each object is distinct, preventing conflicts and security issues.

## 37. How does the Kerberos authentication process works?

Kerberos uses tickets to verify identity without sending passwords over the network, enhancing security.

## 38. What is Prefetch Folder in Windows

The Prefetch folder stores data to speed up the loading of applications, improving performance.

## 39. What does the Export-VM command do?

The `Export-VM` command in PowerShell exports a virtual machine's configuration and data to a specified location.

## 40. What are Federation Services and Certificate Services in an Active Directory environment

* **Federation Services**: Allow single sign-on (SSO) across different organizations or applications.
* **Certificate Services**: Issue digital certificates to verify identities and encrypt communications within the network.
