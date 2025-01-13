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

{% hint style="info" %}
Why is UDP scan slower than TCP Scan?

Since `UDP` is a `stateless protocol` and does not require a three-way handshake like TCP we do not receive any acknowledgment.&#x20;

Consequently, the timeout is much longer, making the whole `UDP scan` (`-sU`) much slower than the `TCP scan` (`-sS`).
{% endhint %}

## -sV (Version Scan)

This method can identify versions, service names, and details about our target.

## -A (Aggressive Scan)

Performs service detection, OS detection, traceroute and uses defaults scripts to scan the target.

## -O (OS Detection)

## --traceroute

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

## --stats-every=5s

Shows the progress of the scan every 5 seconds.

## -v

Increases the verbosity of the scan, which displays more detailed information.

## --script \<example>

Uses all related scripts from specified category.

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

## Nmap Scripting Engine (NSE)

Script Categories:

| **Category** | **Description**                                                                                                                         |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------- |
| `auth`       | Determination of authentication credentials.                                                                                            |
| `broadcast`  | Scripts, which are used for host discovery by broadcasting and the discovered hosts, can be automatically added to the remaining scans. |
| `brute`      | Executes scripts that try to log in to the respective service by brute-forcing with credentials.                                        |
| `default`    | Default scripts executed by using the `-sC` option.                                                                                     |
| `discovery`  | Evaluation of accessible services.                                                                                                      |
| `dos`        | These scripts are used to check services for denial of service vulnerabilities and are used less as it harms the services.              |
| `exploit`    | This category of scripts tries to exploit known vulnerabilities for the scanned port.                                                   |
| `external`   | Scripts that use external services for further processing.                                                                              |
| `fuzzer`     | This uses scripts to identify vulnerabilities and unexpected packet handling by sending different fields, which can take much time.     |
| `intrusive`  | Intrusive scripts that could negatively affect the target system.                                                                       |
| `malware`    | Checks if some malware infects the target system.                                                                                       |
| `safe`       | Defensive scripts that do not perform intrusive and destructive access.                                                                 |
| `version`    | Extension for service detection.                                                                                                        |
| `vuln`       | Identification of specific vulnerabilities.                                                                                             |

### Default Scripts

```bash
sudo nmap <target> -sC
```

### Specific Scripts Category

```bash
sudo nmap <target> --script <category>
```

### Defined Scripts

```bash
sudo nmap <target> --script <script-name>,<script-name>,...
```

