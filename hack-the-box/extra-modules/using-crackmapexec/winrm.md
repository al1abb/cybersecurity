# WinRm

## Password Spray Example with WinRm

```
crackmapexec winrm 10.10.10.1 -u users.txt -p password.txt --no-bruteforce --continue-on-success
```

The target protocol can be a different protocol too. (Ex: smb)
