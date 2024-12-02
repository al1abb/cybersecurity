# AD LDAP 4

Import-Module ActiveDirectory

## Retrieve all user accounts in the domain.

(New-Object DirectoryServices.DirectorySearcher "(objectClass=user)").FindAll() | ForEach-Object { $\_.Properties.samaccountname } Get-ADUser -LDAPFilter "(objectClass=user)" | Select-Object samaccountname

## Find all computer accounts in the domain.

(New-Object DirectoryServices.DirectorySearcher "(objectClass=computer)").FindAll() | ForEach-Object { $\_.Properties.name } Get-ADComputer -LDAPFilter "(objectClass=computer)" | Select-Object Name

## Enumerate all groups in the domain.

(New-Object DirectoryServices.DirectorySearcher "(objectClass=group)").FindAll() | ForEach-Object { $\_.Properties.name } Get-ADGroup -LDAPFilter "(objectClass=group)" | Select-Object Name

## List all organizational units (OUs) in the domain.

(New-Object DirectoryServices.DirectorySearcher "(objectClass=organizationalUnit)").FindAll() | ForEach-Object { $\_.Properties.name } Get-ADObject -LDAPFilter "(objectClass=organizationalUnit)" -SearchBase "DC=marcos,DC=dom" | Select-Object Name

## Retrieve all disabled user accounts.

(New-Object DirectoryServices.DirectorySearcher "(&(objectClass=user)(userAccountControl:1.2.840.113556.1.4.803:=2))").FindAll() | ForEach-Object { $\_.Properties.samaccountname } Get-ADUser -LDAPFilter "(&(objectClass=user)(userAccountControl:1.2.840.113556.1.4.803:=2))" | Select-Object SamAccountName

## List all enabled user accounts.

(New-Object DirectoryServices.DirectorySearcher "(&(objectClass=user)(!(userAccountControl:1.2.840.113556.1.4.803:=2)))").FindAll() | ForEach-Object { $\_.Properties.samaccountname } Get-ADUser -LDAPFilter "(&(objectClass=user)(!(userAccountControl:1.2.840.113556.1.4.803:=2)))" | Select-Object SamAccountName

## Identify users with password expiration disabled.

(New-Object DirectoryServices.DirectorySearcher "(&(objectClass=user)(userAccountControl:1.2.840.113556.1.4.803:=65536))").FindAll() | ForEach-Object { $\_.Properties.samaccountname } Get-ADUser -LDAPFilter "(&(objectClass=user)(userAccountControl:1.2.840.113556.1.4.803:=65536))" | Select-Object SamAccountName

## List all user accounts with blank email addresses.

(New-Object DirectoryServices.DirectorySearcher "(&(objectClass=user)(!(mail=_)))").FindAll() | ForEach-Object { $\_.Properties.samaccountname } Get-ADUser -LDAPFilter "(&(objectClass=user)(!(mail=_)))" | Select-Object SamAccountName

## Enumerate all users in the "Domain Admins" group.

(New-Object DirectoryServices.DirectorySearcher "(&(objectClass=group)(cn=Domain Admins))").FindOne().Properties\["member"]

## Retrieve users with "Admin" in their display name.

### in AD there are no such users so test with string salam

(New-Object DirectoryServices.DirectorySearcher "(&(objectClass=user)(displayName=_sal_))").FindAll() | ForEach-Object { $\_.Properties\["samaccountname"] } Get-ADUser -LDAPFilter "(displayName=_Admin_)" | Select-Object SamAccountName

## Find all user accounts created within the last 30 days.

### Calculate the date 30 days ago and convert it to LDAP format

$daysAgo = (Get-Date).AddDays(-30).ToString("yyyyMMddHHmmss.0Z")

### Create a DirectorySearcher with the correct query

$searcher = New-Object DirectoryServices.DirectorySearcher "(&(objectClass=user)(whenCreated>=$daysAgo))" $searcher.PropertiesToLoad.Add("samaccountname")

### Execute the search and output results

$searcher.FindAll() | ForEach-Object { $\_.Properties\["samaccountname"] }

## Identify all groups containing "Finance" in the name.

(New-Object DirectoryServices.DirectorySearcher "(&(objectClass=group)(name=_Finance_))").FindAll() | ForEach-Object { $\_.Properties.name } Get-ADGroup -LDAPFilter "(cn=_Finance_)" | Select-Object Name

## Retrieve all users in a specific OU (e.g., Sales).

(\[ADSI]"LDAP://OU=IT Department,DC=marcos,DC=dom").psbase.Children | Where-Object { $\_.objectClass -eq 'user' } | Select-Object -ExpandProperty samAccountName Get-ADUser -Filter \* -SearchBase "OU=IT Department,DC=marcos,DC=dom" | Select-Object SamAccountName

## List all users with logon scripts.

(New-Object DirectoryServices.DirectorySearcher "(&(objectClass=user)(scriptPath=\*))").FindAll() | ForEach-Object { $_.Properties.samaccountname } (\[ADSI]"LDAP://DC=marcos,DC=dom").psbase.Children | Where-Object { $_.scriptPath } | Select-Object -ExpandProperty samAccountName

## Find all computers running Windows 10.

(New-Object DirectoryServices.DirectorySearcher "(&(objectClass=computer)(operatingSystem=Windows 10))").FindAll() | ForEach-Object { $\_.Properties.name }

## Identify users with last logon timestamp older than 90 days.

$90DaysAgo = (Get-Date).AddDays(-90).ToFileTime() (New-Object DirectoryServices.DirectorySearcher "(&(objectClass=user)(lastLogon<=$90DaysAgo))").FindAll() | ForEach-Object { $\_.Properties.samaccountname }

## List service accounts (accounts with servicePrincipalName set).

(New-Object DirectoryServices.DirectorySearcher "(&(objectClass=user)(servicePrincipalName=_))").FindAll() | ForEach-Object { $\_.Properties.samaccountname } Get-ADObject -LDAPFilter "(servicePrincipalName=_)" -SearchBase "CN=Users,DC=marcos,DC=dom" | Select-Object Name

## Retrieve all groups that contain members (i.e., non-empty groups).

(New-Object DirectoryServices.DirectorySearcher "(&(objectClass=group)(member=\*))").FindAll() | ForEach-Object { $\_.Properties.name }

## Identify all privileged groups, such as "Domain Admins" or "Enterprise Admins".

(New-Object DirectoryServices.DirectorySearcher "(&(objectClass=group)(|(cn=Domain Admins)(cn=Enterprise Admins)))").FindAll() | ForEach-Object { $\_.Properties.name } Get-ADGroup -LDAPFilter "(|(cn=Domain Admins)(cn=Enterprise Admins))" | Select-Object Name

## Enumerate all GPOs (Group Policy Objects) in the domain.

(New-Object DirectoryServices.DirectorySearcher "(&(objectClass=groupPolicyContainer))").FindAll() | ForEach-Object { $\_.Properties.displayname } Get-ADObject -LDAPFilter "(objectClass=groupPolicyContainer)" -Properties displayName | Select-Object displayName

## Find all accounts with delegated permissions on OUs.

(New-Object DirectoryServices.DirectorySearcher "(&(objectClass=user)(adminCount=1))").FindAll() | ForEach-Object { $\_.Properties.samaccountname }

## Retrieve all accounts with non-default password policies.

(\[adsisearcher]"(&(objectClass=user)(objectCategory=person)(msDS-PSOApplied=\*))").FindAll() | ForEach-Object { $\_.Properties.samaccountname }

## Identify all accounts with Kerberos pre-authentication disabled.

(New-Object DirectoryServices.DirectorySearcher "(&(objectClass=user)(userAccountControl:1.2.840.113556.1.4.803:=4194304))").FindAll() | ForEach-Object { $\_.Properties.samaccountname }

## List all accounts with the "Do Not Require Kerberos Preauthentication" flag.

(New-Object DirectoryServices.DirectorySearcher "(&(objectClass=user)(userAccountControl:1.2.840.113556.1.4.803:=1048576))").FindAll() | ForEach-Object { $\_.Properties.samaccountname }

(\[adsisearcher]"(&(objectClass=user)(objectCategory=person)(userAccountControl:1.2.840.113556.1.4.803:=4194304))").FindAll() | ForEach-Object { $\_.Properties.samaccountname }

(New-Object DirectoryServices.DirectorySearcher "(&(objectClass=user)(userAccountControl:1.2.840.113556.1.4.803:=4194304))").FindAll() | ForEach-Object { $\_.Properties.samaccountname }

## Enumerate all AD sites and subnets.

(New-Object DirectoryServices.DirectorySearcher "(&(objectClass=site))").FindAll() | ForEach-Object { $_.Properties.name }; (New-Object DirectoryServices.DirectorySearcher "(&(objectClass=subnet))").FindAll() | ForEach-Object { $_.Properties.name } Get-ADReplicationSite -Filter \* | Select-Object Name Get-ADObject -LDAPFilter "(|(objectClass=site)(objectClass=subnet))" -SearchBase "CN=Sites,CN=Configuration,DC=marcos,DC=dom" | Select-Object Name
