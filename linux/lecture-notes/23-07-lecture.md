# 23/07. VM networking + start modes

VMware does not have NAT from the image. It has NAT but it does the job of NAT Network from the image below:

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption><p>Networking Modes in VMs</p></figcaption></figure>

Virtual Box has all from the above image. We will mostly use NAT Network and Bridged

Telnet = unsecure SSH (Running on port 23)

{% hint style="info" %}
SSH uses RSA key fingerprinting for auth. For the first time authentications. SSH has a list of known hosts

* If you answer yes at the prompt (asking to verify the machine’s identity), the RSA key fingerprint of the remote machine will be stored on your local system

This secures you against ARP Spoofing.
{% endhint %}

{% hint style="info" %}
On a bridged network, first randomize MAC or problems regarding the ARP table will be there and the IP address from DHCP will be the same causing collisions
{% endhint %}

### Start modes

Normal Start - with GUI

Headless - CMD only

Detachable - Hybrid
