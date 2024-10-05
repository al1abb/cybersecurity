# DNS Steps

## 1. **User Enters Domain Name**:&#x20;

The process starts when a user types a domain name (e.g., `example.com`) into a web browser

## **2. Browser Checks Cache**:&#x20;

The browser first checks its local cache to see if it has a recent DNS record for that domain. If it does, it uses this cached record to fetch the IP address.

## **3. Operating System Cache**:&#x20;

If the browser cache doesn't have the DNS record, the browser requests the IP address from the operating system's DNS resolver cache.

## **4. Query to DNS Resolver**:&#x20;

If the IP address isn't cached on the local system, the DNS resolver sends a query to a recursive DNS resolver, typically provided by the user's ISP or a third-party DNS service (e.g., Google DNS, Cloudflare).

## **5. Check Recursive Resolver's Cache**:&#x20;

The recursive resolver checks its own cache. If the domain has been requested recently, the IP address is returned immediately.

## **6. Query to Root DNS Servers**:

If the IP address isn't cached, the recursive resolver sends a query to one of the **Root DNS Servers**. These servers are the top of the DNS hierarchy and help direct the request further down the chain.

## **7. Root DNS Server Responds**:&#x20;

The root server responds with the address of a **Top-Level Domain (TLD) DNS server** (e.g., for `.com` domains, it directs the query to the .com TLD server).

## **8. Query to TLD DNS Server**:&#x20;

The recursive resolver then queries the TLD server (e.g., the `.com` DNS server for `example.com`).

## **9. TLD Server Responds**:&#x20;

The TLD server responds with the address of the **Authoritative DNS Server** for the domain (e.g., the DNS server for `example.com`).

## **10. Query to Authoritative DNS Server**:&#x20;

The recursive resolver sends a query to the authoritative DNS server for the domain.

## **11. Authoritative DNS Server Responds**:&#x20;

The authoritative DNS server responds with the IP address associated with the domain (e.g., `93.184.216.34` for `example.com`).

## **12. IP Address Returned to Browser**:&#x20;

The recursive resolver returns the IP address to the browser.

## **13. Browser Connects to the Server**:&#x20;

The browser uses the IP address to send a request to the web server hosting the website, and the website content is retrieved and displayed to the user.

## Short Fireship Video:

{% embed url="https://youtu.be/UVR9lhUGAyU" %}
DNS | Fireship
{% endembed %}
