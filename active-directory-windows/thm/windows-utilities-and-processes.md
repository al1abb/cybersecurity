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

