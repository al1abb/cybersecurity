# Lab 1 Notes

3\)

Version: 10.0.19045 Build 19045

System Directory: C:\Windows\system32

Motherboard: Manufacturer = Intel Corporation, Product=440BX Desktop Reference Platform



You can use Get-ComputerInfo directly from PowerShell  to get system info



4\) System information binary and its path

msinfo32.exe

Location: C:\Windows\System32\msinfo32.exe



5\) StartMenuExperienceHost.exe has the lowest PID which is 3844



6\) Usernames list:

Ali

DWM-2

SYSTEM

NETWORK SERVICE



7\)

a) It contains 2 sub keys, 7 files

b) ItemName binary is responsible for running .txt files



8\)

Microsoft Management Console (mmc.exe)



9\)

Through Advanced Audit Policy Configuration, you find detailed tracking and then go to audit process creation. Double click and apply it for both success and failure.

You can check using the Audit Events bar in detailed tracking&#x20;



Powershell notes:

cmd - Opens cmd

regedit - Opens registry editor

mmc - Opens Microsot Management Console

services -  Was not working

$env:Path - ENV variables and their path.

secpol - Local Security Policy

eventvwr - Opens Event Viewer (Windows logs is here)
