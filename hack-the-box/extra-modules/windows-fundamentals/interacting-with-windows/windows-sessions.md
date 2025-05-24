# Windows Sessions

### **Interactive**

An interactive, or local logon session, is initiated by a user authenticating to a local or domain system by entering their credentials. An interactive logon can be initiated by logging directly into the system, by requesting a secondary logon session using the `runas` command via the command line, or through a Remote Desktop connection.

### **Non-interactive**

Non-interactive accounts in Windows differ from standard user accounts as they do not require login credentials. There are 3 types of non-interactive accounts: the Local System Account, Local Service Account, and the Network Service Account. Non-interactive accounts are generally used by the Windows operating system to automatically start services and applications without requiring user interaction. These accounts have no password associated with them and are usually used to start services when the system boots or to run scheduled tasks.

There are differences between the three types of accounts:

<table><thead><tr><th width="232">Account</th><th>Description</th></tr></thead><tbody><tr><td>Local System Account</td><td>Also known as the <code>NT AUTHORITY\SYSTEM</code> account, this is the most powerful account in Windows systems. It is used for a variety of OS-related tasks, such as starting Windows services. This account is more powerful than accounts in the local administrators group.</td></tr><tr><td>Local Service Account</td><td>Known as the <code>NT AUTHORITY\LocalService</code> account, this is a less privileged version of the SYSTEM account and has similar privileges to a local user account. It is granted limited functionality and can start some services.</td></tr><tr><td>Network Service Account</td><td>This is known as the <code>NT AUTHORITY\NetworkService</code> account and is similar to a standard domain user account. It has similar privileges to the Local Service Account on the local machine. It can establish authenticated sessions for certain network services.</td></tr></tbody></table>
