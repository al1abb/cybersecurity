# PowerShell Useful Commands

2 main commands:

## **Get-Command**

Gets all cmdlets on the current computer:

```powershell
Get-Command Verb-Noun
Get-Command Verb-*
Get-Command *-Noun
```

## **Get-Help \[Command Name]**

Displays info about a cmdlet:

```powershell
Get-Help Get-Command -Examples
```

## PWD in Linux

```powershell
Get-Location
```

## Select-Object

Listing directories and selecting only mode and name:

```powershell
Get-ChildItem | Select-Object -Property Mode, Name
```

## Where-Object

Checking stopped processes:

```powershell
Get-Service | Where-Object -Property Status -eq Stopped
```

Other examples:

```powershell
Verb-Noun | Where-Object -Property PropertyName -operator Value

Verb-Noun | Where-Object {$_.PropertyName -operator Value}
```

## Sort-Object

Sorting the list of directories:

```powershell
Get-ChildItem | Sort-Object
```

## Getting Count of Something

Group the result in () brackets and end it with `.Count`

Example:

```powershell
# Get count of files and folders in current directory
(Get-ChildItem).Count
```

## Finding Files

Find a file called `interesting-file`

```powershell
Get-ChildItem “*interesting-file*” -Path C:\ -Recurse -ErrorAction SilentlyContinue
```

Find and show content of `interesting-file`

```powershell
Get-ChildItem “*interesting-file*” -Path C:\ -Recurse -ErrorAction SilentlyContinue | Get-Content
```

## Find currently installed cmdlets (Cmdlets only)

```powershell
(Get-Command | Where-Object {$_.CommandType -eq “Cmdlet”}).Count
```

## Get MD5 Hash of a file

Get file hash using `Get-FileHash`:

```powershell
Get-ChildItem “*interesting-file*” -Path C:\ -Recurse -ErrorAction SilentlyContinue | Get-FileHash -Algorithm MD5
```

## Check if a path exists

```powershell
if(Set-Location C:\Users\Administrator\Documents\Passwords)
{Write-Host "It exists!"}
Else{Write-Host "The path doesn't exist."}
```

## Make web requests

```powershell
Invoke-WebRequest -Uri "https://example.com"
```

