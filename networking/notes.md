# Notes

## 18/07

{% hint style="info" %}
You never find encryption on layer 6 in OSI Model. Some old books say it
{% endhint %}

{% hint style="info" %}
Station = L2

Host = L3
{% endhint %}

{% hint style="info" %}
Hub works in L1

Switch works in L2

Router in L3

Firewall is minimum L4
{% endhint %}

{% hint style="info" %}
Hubs might collide

Switches handle collision. Research CAM tables
{% endhint %}

{% hint style="success" %}
Star topology is best topology
{% endhint %}

## 24/07

### Difference between modems and routers

{% hint style="info" %}
Modem transfers analog to digital and vice versa. Can be considered a router.

Modems are routers, but not all routers are modems

Mo-dem: Modulator - Demodulator
{% endhint %}

### NIC Types

There are 2 NIC types: **Wired and Wireless.** You can have both or one or none

### Duplexing

Simplex example: Satellite

Half-duplex example: Walkie-talkie, Wi-Fi

Full-duplex: Talking on the phone. Both sides can talk at the same time

{% hint style="info" %}
Hub is half-duplex

Switch is full-duplex
{% endhint %}

{% hint style="info" %}
Microwave can interfere with WiFi signals
{% endhint %}

### Copper

Unshielded Twisted Pair(UTP) cables have problems with EMI, RF. Electromagnetic Interference, Radio Frequency

Shielded TP cables are more resistant to EMI. Uses aluminum foil

Coaxial Cable: The best. Thick copper in the middle + insulation

Faraday cage. Cancels out all interference. Used in forensics. It even cancels WIFI signals

{% hint style="info" %}
For twisted pair cables that need to go over 100m, you can use repeaters, otherwise, latency gets high
{% endhint %}

### WiMAX, LTE

WiMAX (point to multipoint) might be used for mobile network towers

LTE is interceptable but its harder than WiFi
