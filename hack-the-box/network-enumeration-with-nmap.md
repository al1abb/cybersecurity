# Network Enumeration with Nmap

## -sS (TCP-SYN Stealth Scan)

* If our target sends a `SYN-ACK` flagged packet back to us, Nmap detects that the port is `open`.
* If the target responds with an `RST` flagged packet, it is an indicator that the port is `closed`.
* If Nmap does not receive a packet back, it will display it as `filtered`. Depending on the firewall configuration, certain packets may be dropped or ignored by the firewall.

## -PE

Performs the ping scan by using 'ICMP Echo requests' against the target.

## --reason

Displays the reason for specific result.

## --packet-trace

Shows all packets sent and received
