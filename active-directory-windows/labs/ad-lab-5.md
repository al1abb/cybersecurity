# AD Lab 5

## Lab 5 Part 1. AD Simple Enumeration

## AD Initial Enumeration

## Print out the users in the domain.

\#net user /domain

\#net user salam /domain

## Check for groups

\#net group /domain

## Another strategy is to look for groups that are inside high privileged groups

\#$highPrivilegedGroups = "Domain Admins", "Enterprise Admins", "Administrators"; #$highPrivilegedGroups | ForEach-Object { Get-ADGroupMember -Identity $\_ | Where-Object { $_.objectClass -eq 'group' } | ForEach-Object { "$($_.Name) is nested in $\_" } }

## AD Recycle Bin

\#Get-ADObject -filter 'isDeleted -eq $true' -includeDeletedObjects -Properties \*

## Lab 5 Part 2. Using .NET libraries?

## Query AD FSMO Roles

\#\[System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain() #netdom query fsmo

## Get Current Forest

\#\[System.DirectoryServices.ActiveDirectory.Forest]::GetCurrentForest()

## Get Current Domain

\#\[System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()

## Get All Trust Relationships

\#(\[System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()).GetAllTrustRelationships()

## Global Catalogs

\#\[System.DirectoryServices.ActiveDirectory.Forest]::GetCurrentForest().GlobalCatalogs

## Lab 5 Part 3. Script that helps with AD Enumeration

<#

## Store the domain object in the $domainObj variable

$domainObj = \[System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()

## Store the PdcRoleOwner name to the $PDC variable

$PDC = $domainObj.PdcRoleOwner.Name

## Store the Distinguished Name variable into the $DN variable

$DN = (\[adsi]'').distinguishedName

$direntry = New-Object System.DirectoryServices.DirectoryEntry($LDAP)

$dirsearcher = New-Object System.DirectoryServices.DirectorySearcher($direntry)

$dirsearcher.filter="samAccountType=805306368"

### $dirsearcher.FindAll()

$result = $dirsearcher.FindAll()

Foreach($obj in $result) { Foreach($prop in $obj.Properties) { $prop }

Write-Host "-------------------------------" }

\#>
