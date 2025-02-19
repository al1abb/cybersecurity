# Windows Security

Security is a critical topic in Windows operating systems. Windows systems have many moving parts that present a vast attack surface. Due to the many built-in applications, features, and layers of settings, Windows systems can be easily misconfigured, thus opening them up to attack even if they are fully patched.

It has many built-in features that can be abused and has suffered from a wide variety of critical vulnerabilities, resulting in widely used and very effective remote and local exploits.

Microsoft has improved upon Windows security over the years. As our world's interconnectedness continues to expand and attackers become more sophisticated, Microsoft has continued to add new features that can be used by systems administrators to harden systems and actively block and detect attempts at intrusion and misuse.

Windows follows certain security principles. These are units in the system that can be authorized or authenticated for a particular action. These units include users, computers on the network, threads, or processes. The principles are designed to make it more difficult for attackers or malicious software to gain unauthorized access and exploit the system in an unintended way.

## Security Identifier (SID)

Each of the security principals on the system has a unique security identifier (SID). The system automatically generates SIDs. This means that even if, for example, we have two identical users on the system, Windows can distinguish the two and their rights based on their SIDs. SIDs are string values with different lengths, which are stored in the security database. These SIDs are added to the user's access token to identify all actions that the user is authorized to take.

A SID consists of the Identifier Authority and the Relative ID (RID). In an Active Directory (AD) domain environment, the SID also includes the domain SID.

```powershell
PS C:\htb> whoami /user

USER INFORMATION
----------------

User Name           SID
=================== =============================================
ws01\bob S-1-5-21-674899381-4069889467-2080702030-1002
```

The SID is broken down into this pattern.

```powershell
(SID)-(revision level)-(identifier-authority)-(subauthority1)-(subauthority2)-(etc)
```

Let's break down the SID piece by piece.

<table data-header-hidden><thead><tr><th></th><th width="190"></th><th></th></tr></thead><tbody><tr><td><strong>Number</strong></td><td><strong>Meaning</strong></td><td><strong>Description</strong></td></tr><tr><td>S</td><td>SID</td><td>Identifies the string as a SID.</td></tr><tr><td>1</td><td>Revision Level</td><td>To date, this has never changed and has always been <code>1</code>.</td></tr><tr><td>5</td><td>Identifier-authority</td><td>A 48-bit string that identifies the authority (the computer or network) that created the SID.</td></tr><tr><td>21</td><td>Subauthority1</td><td>This is a variable number that identifies the user's relation or group described by the SID to the authority that created it. It tells us in what order this authority created the user's account.</td></tr><tr><td>674899381-4069889467-2080702030</td><td>Subauthority2</td><td>Tells us which computer (or domain) created the number</td></tr><tr><td>1002</td><td>Subauthority3</td><td>The RID that distinguishes one account from another. Tells us whether this user is a normal user, a guest, an administrator, or part of some other group</td></tr></tbody></table>
