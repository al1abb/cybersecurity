# WPScan

Enumerate Users:

```bash
wpscan --url http://targetsite.com --enumerate u
```

Brute Force Passwords (w/ rockyou):

```bash
wpscan --url http://targetsite.com --usernames /path/to/usernames.txt --passwords /path/to/passwords.txt
```
