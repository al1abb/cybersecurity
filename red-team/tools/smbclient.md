# smbclient

Get SMB shares for a specific user:

```bash
smbclient -L spookysec.local -U svc-admin
```

Get inside a specific SMB share:

```bash
smbclient //spookysec.local/backup -U svc-admin
```

