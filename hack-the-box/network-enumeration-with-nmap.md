# Network Enumeration with Nmap

## Port States

| **State**          | **Description**                                                                                                                                                                                         |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `open`             | This indicates that the connection to the scanned port has been established. These connections can be **TCP connections**, **UDP datagrams** as well as **SCTP associations**.                          |
| `closed`           | When the port is shown as closed, the TCP protocol indicates that the packet we received back contains an `RST` flag. This scanning method can also be used to determine if our target is alive or not. |
| `filtered`         | Nmap cannot correctly identify whether the scanned port is open or closed because either no response is returned from the target for the port or we get an error code from the target.                  |
| `unfiltered`       | This state of a port only occurs during the **TCP-ACK** scan and means that the port is accessible, but it cannot be determined whether it is open or closed.                                           |
| `open\|filtered`   | If we do not get a response for a specific port, `Nmap` will set it to that state. This indicates that a firewall or packet filter may protect the port.                                                |
| `closed\|filtered` | This state only occurs in the **IP ID idle** scans and indicates that it was impossible to determine if the scanned port is closed or filtered by a firewall.                                           |

## -sT (TCP Connect Scan)

Uses the TCP three-way handshake to determine if a specific port on a target host is open or closed. The scan sends an `SYN` packet to the target port and waits for a response. It is considered open if the target port responds with an `SYN-ACK` packet and closed if it responds with an `RST` packet.

Triggers logs. Can be caught by IDS/IPS systems. But is more accurate

## -sS (TCP-SYN Stealth Scan)

* If our target sends a `SYN-ACK` flagged packet back to us, Nmap detects that the port is `open`.
* If the target responds with an `RST` flagged packet, it is an indicator that the port is `closed`.
* If Nmap does not receive a packet back, it will display it as `filtered`. Depending on the firewall configuration, certain packets may be dropped or ignored by the firewall.

## -sU (UDP Scan)

Performs a UDP scan.

## -sV (Version Scan)

This method can identify versions, service names, and details about our target.

## --top-ports=10

Scans the specified top ports that have been defined as most frequent.

## -n

Disables DNS resolution.

## --disable-arp-ping

Disables ARP ping

## -PE

Performs the ping scan by using 'ICMP Echo requests' against the target.

## --reason

Displays the reason for specific result.

## --packet-trace

Shows all packets sent and received

{% hint style="info" %}
Why is UDP scan slower than TCP Scan?

Since `UDP` is a `stateless protocol` and does not require a three-way handshake like TCP we do not receive any acknowledgment.&#x20;

Consequently, the timeout is much longer, making the whole `UDP scan` (`-sU`) much slower than the `TCP scan` (`-sS`).
{% endhint %}

## Saving the Results

* Normal output (`-oN`) with the `.nmap` file extension
* Grepable output (`-oG`) with the `.gnmap` file extension
* XML output (`-oX`) with the `.xml` file extension
* We can also specify the option (`-oA`) to save the results in all formats.

{% hint style="info" %}
With the XML output, we can easily create HTML reports that are easy to read, even for non-technical people. This is later very useful for documentation, as it presents our results in a detailed and clear way. To convert the stored results from XML format to HTML, we can use the tool `xsltproc`.
{% endhint %}

```bash
xsltproc target.xml -o target.html
```

