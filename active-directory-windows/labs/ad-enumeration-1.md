# AD Enumeration 3

## Hostnames and IP Addresses

```powershell
Get-ADComputer -Filter * 

hostname
```

## Open Ports and Services

```powershell
Invoke-Command -ComputerName commando -ScriptBlock { netstat -ano }
```

## Operating System Version

```powershell
Get-ADComputer -Filter * | ForEach-Object { Get-CimInstance -ClassName Win32_OperatingSystem -ComputerName $_.Name | Select-Object PSComputerName, Caption, Version, BuildNumber }
```

## Installed Software (Query the registry)

```powershell
Get-ADComputer -Filter * | ForEach-Object { $computer = $_.Name Invoke-Command -ComputerName $computer -ScriptBlock { Get-ItemProperty -Path "HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall*", "HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall*" | Select-Object PSComputerName, DisplayName, DisplayVersion, Publisher, InstallDate } -ErrorAction SilentlyContinue }
```

## Installed Software (Oneliner)

```powershell
Get-ItemProperty "HKLM:\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall*" | select displayname
```

## Patch Levels and Missing Updates: - Check for missing patches and updates.

```powershell
Get-ADComputer -Filter * | ForEach-Object { $computer = $_.Name Invoke-Command -ComputerName $computer -ScriptBlock { $updateSession = New-Object -ComObject Microsoft.Update.Session $updateSearcher = $updateSession.CreateUpdateSearcher() $searchResult = $updateSearcher.Search("IsInstalled=0 AND Type='Software'") foreach ($update in $searchResult.Updates) { [PSCustomObject]@{ ComputerName = $env:COMPUTERNAME UpdateTitle = $update.Title KBArticle = ($update.KBArticleIDs -join ", ") Severity = $update.MsrcSeverity Description = $update.Description } } } -ErrorAction SilentlyContinue }
```

## Running Processes

```powershell
Get-Process
```

## Scheduled Tasks

```powershell
Get-ScheduledTask | Select-Object TaskName, State, LastRunTime, NextRunTime, Action
```

## Scheduled Tasks (Long version)

```powershell
Get-ADComputer -Filter * | ForEach-Object { Invoke-Command -ComputerName $_.Name -ScriptBlock { Get-ScheduledTask | Select-Object TaskName, State, LastRunTime, NextRunTime, Action } -ErrorAction SilentlyContinue }
```

## Local User Accounts

```powershell
Get-LocalUser 

Get-ADComputer -Filter * | ForEach-Object { Invoke-Command -ComputerName $_.Name -ScriptBlock { Get-LocalUser | Select-Object Name, Enabled, Description } } -ErrorAction SilentlyContinue
```

## After installing recon module from powersploit

```powershell
Get-NetUser
```

## Local Group Memberships

```powershell
Get-LocalGroupMember ITGroup 

Get-NetGroup
```

## Active Network Connections

```powershell
netstat -ano 

Get-NetTCPConnection | Format-Table -Property LocalAddress, LocalPort, RemoteAddress, RemotePort, State, OwningProcess
```

## Shares and Permissions (Can use bloodhound for shares and permissions)

```powershell
Get-SmbShare 
net share
```

## Registry Settings

```powershell
Get-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System' | Select-Object EnableLUA, ConsentPromptBehaviorAdmin 

reg query HKLM\SYSTEM\CurrentControlSet\Services\ /s | findstr /i "security"
```

## Firewall Rules

```powershell
Get-NetFirewallRule | Format-Table Name, DisplayName, Enabled, Action, Direction, Profile
```

### After importing powerview

```powershell
Get-NetFirewallRule | Where-Object {$_.Enabled -eq "True"}
```

## Audit Policies

```powershell
auditpol /get /category:*
```

## Event logs

```powershell
Get-EventLog -LogName Security
```

## Group Policy Objects (GPOs)

```powershell
Get-GPO -All | Select-Object DisplayName, Id, CreationTime, ModificationTime
```

## Antivirus and Security Software

```powershell
Get-MpPreference
```

## Disk and File System Information

### Check the file system integrity

```powershell
chkdsk C:
```

```powershell
Get-PSDrive -PSProvider FileSystem 
Get-Volume 
Get-Disk
```

## System Uptime

```powershell
systeminfo | find "System Boot Time" 
(Get-CimInstance -ClassName Win32_OperatingSystem).LastBootUpTime 
[System.Environment]::TickCount / 1000 / 60 / 60 / 24
```

## Environment Variables

```powershell
Get-ChildItem Env:
```

## Active Directory Enumeration

## Domain Controllers

```powershell
Get-ADDomainController -Filter *
```

## Domain Trusts

```powershell
Get-NetDomainTrust 
Get-NetForestTrust
```

## Domain and Forest Functional Levels

```powershell
Get-ADDomain | Select-Object Name, DomainFunctionalLevel 

Get-ADForest | Select-Object Name, ForestFunctionalLevel
```

## AD Sites and Subnets

```powershell
Get-ADReplicationSite | Select-Object Name, Location 

Get-ADReplicationSubnet -Filter *
```

## Organizational Units (OUs)

```powershell
Get-ADOrganizationalUnit -Filter * | Select-Object Name, DistinguishedName 
```

```powershell
Get-ADOrganizationalUnit -Filter *
```

## Domain Users

```powershell
Get-ADUser -Filter * -Properties * 

Get-NetUser
```

## Domain Groups

```powershell
Get-NetGroup -Members 
```

```powershell
Get-NetGroupMember * 
```

```powershell
Get-NetGroup | ForEach-Object { Get-NetGroupMember -Identity $.SamAccountName | Select-Object @{Name="GroupName";Expression={$}}, @{Name="MemberName";Expression={$.Name}}, @{Name="MemberSAM";Expression={$.SamAccountName}}, @{Name="MemberType";Expression={$_.objectClass}} }
```

## Service Principal Names (SPNs)

```powershell
Get-ADUser -Filter * -Property servicePrincipalName | Where-Object { $.servicePrincipalName -ne $null } | Select-Object SamAccountName, servicePrincipalName
```

```powershell
Get-ADComputer -Filter * -Property servicePrincipalName | Where-Object { $.servicePrincipalName -ne $null } | Select-Object Name, servicePrincipalName 
```

```powershell
Get-ADComputer -Identity COMMANDO -Properties ServicePrincipalNames |Select-Object -ExpandProperty ServicePrincipalNames
```

## Group Policy Objects (GPOs)

```powershell
Get-NetGPO
```

```powershell
Get-NetGPOGroup
```

## Domain Admins and Privileged Accounts

```powershell
Get-NetGroupMember "Domain Admins"
```

```powershell
Get-NetGroupMember "Administrators"
```

## Password Policies

```powershell
Get-ADDefaultDomainPasswordPolicy | Select-Object -Property MaxPasswordAge, MinPasswordAge, MinPasswordLength, PasswordHistoryCount, ComplexityEnabled, LockoutThreshold, LockoutDuration 
```

```powershell
Get-NetDomain | Select-Object -Property Name, MaxPasswordAge, MinPasswordAge, MinPasswordLength, PasswordHistoryCount, ComplexityEnabled, LockoutThreshold, LockoutDuration 
```

```powershell
Get-DomainPolicy
```

## Account Lockout Policies

```powershell
Get-DomainPolicy | Select-Object -Property LockoutThreshold, LockoutDuration, LockoutObservationWindow 
```

```powershell
Get-ADDefaultDomainPasswordPolicy | Select-Object -Property LockoutThreshold, LockoutDuration, LockoutObservationWindow
```

## Kerberos Policies

```powershell
Get-DomainPolicy | Select-Object -Property MaxTicketAge, MaxRenewableAge, TicketGrantingTicketLifetime 
```

```powershell
Get-ADDefaultDomainPasswordPolicy | Select-Object -Property MaxTicketAge, MaxRenewableAge 
```

```powershell
Get-DomainPolicy
```

## LDAP Configuration

```powershell
Get-ADDomain | Select-Object -Property Name, DNSRoot, LDAPDisplayName, DistinguishedName
```

```powershell
Get-Domain | Select-Object -Property Name, DistinguishedName, DomainControllers
```

```powershell
Get-ADDomainController -Filter * | Select-Object -Property Name, IPv4Address, IsGlobalCatalog, Site
```

## Active Directory Certificate Services (AD CS)

```powershell
Get-ADObject -Filter { ObjectClass -eq "pKICertificateTemplate" } -SearchBase "CN=Certificate Templates,CN=Public Key Services,CN=Services,CN=Configuration,DC=marcos,DC=dom" | Select-Object Name, DistinguishedName
```

```powershell
Get-WmiObject -Namespace "root\CIMv2" -Class "Win32_Service" | Where-Object { $_.Name -like "CertSvc" }
```

## Computer Accounts: List all domain-joined computer accounts

```powershell
Get-ADComputer -Filter * | Select-Object Name, DistinguishedName
```

## SID History

```powershell
Get-ADUser -Filter { SIDHistory -ne "$null" } -Property SIDHistory | Select-Object Name, SIDHistory
```

```powershell
Get-ADComputer -Filter { SIDHistory -ne "$null" } -Property SIDHistory | Select-Object Name, SIDHistory
```

## AdminSDHolder Protected Accounts

```powershell
Get-ADUser -Filter { AdminCount -eq 1 } -Properties AdminCount | Select-Object Name, SamAccountName, DistinguishedName
```

```powershell
Get-ADGroup -Filter { AdminCount -eq 1 } -Properties AdminCount | Select-Object Name, SamAccountName, DistinguishedName
```

## DNS Configuration

```powershell
Get-DnsServerZone
```

```powershell
Get-DnsServerResourceRecord -ZoneName "marcos.dom"
```

```powershell
Get-DnsServerResourceRecord -ZoneName "marcos.dom" | Where-Object { $_.RecordType -eq "SRV" }
```

## AD Replication Status

```powershell
Get-ADReplicationFailure -Scope Domain | Select-Object Server, FailureCount, FirstFailureTime, LastFailureTime, FailureType 
```

```powershell
Get-ADReplicationPartnerMetadata -Target "commando" | Select-Object Partner, LastReplicationResult, LastReplicationTime
```

