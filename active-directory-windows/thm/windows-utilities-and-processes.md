# Windows Utilities & Processes

## Utilities

* Local User and Group Management (**lusrmgr.msc**) - You can view local users and groups from here
* System Configuration (**msconfig**) - Is for advanced troubleshooting
* Computer Management (**compmgmt**)
* System Info (**msinfo32**)

## Processes

* System - Always has a PID of 4. Runs in kernel mode
* Session Manager Subsystem (**smss.exe**) - This process, also known as the Windows Session Manager, is responsible for creating new sessions. It is the first user-mode process started by the kernel.
* Client Server Runtime Process (**csrss.exe**) - User-mode side of the Windows subsystem. This process is always running and is critical to system operation. If this process is terminated by chance, it will result in system failure. This process is responsible for the Win32 console window and process thread creation and deletion
* Windows Initialization Process (**wininit.exe**) - Responsible for launching services.exe (Service Control Manager), lsass.exe (Local Security Authority), and lsaiso.exe within Session 0
* Service Control Manager (SCM) (**services.exe**). Its primary responsibility is to handle system services: loading services, interacting with services, and starting or ending services. It maintains a database that can be queried using a Windows built-in utility, `sc.exe`
* Service Host (Host Process for Windows Services) (**svchost.exe**), is responsible for hosting and managing Windows services.
* Local Security Authority Subsystem Service (LSASS) (**lsass.exe**) is a process in Microsoft Windows operating systems that is responsible for enforcing the security policy on the system. It verifies users logging on to a Windows computer or server, handles password changes, and creates access tokens. It also writes to the Windows Security Log

