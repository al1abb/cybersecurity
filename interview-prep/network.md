# Network

## 1) Take an example situation and describe the transmission of data throughout OSI layers.

**Example Situation: Browsing a Website (HTTP Request)**

Let's consider a scenario where a user accesses a website by entering a URL in their browser.

#### Layer 7: Application Layer

The browser acts as the client, generating an HTTP request to fetch the webpage. This request includes details like the URL, headers, and any data that needs to be sent (e.g., form data).

#### Layer 6: Presentation Layer

The data from the Application layer is converted into a format suitable for transmission. For web browsing, this might involve encrypting the HTTP request into HTTPS using SSL/TLS to ensure secure communication.

#### Layer 5: Session Layer

The session layer establishes, manages, and terminates the connection between the client and server. In our example, the session layer sets up an HTTP session with the server, ensuring a consistent, ordered exchange of data.

#### Layer 4: Transport Layer

The Transport layer breaks down the data into smaller segments. For TCP, which is used in HTTP, it assigns sequence numbers to segments, ensuring data is received and reassembled correctly at the destination. It also handles error-checking and retransmission of lost segments.

#### Layer 3: Network Layer

The Network layer handles routing the data packets across the network. It assigns the source and destination IP addresses to each packet. For our scenario, it routes the data from the user's IP address to the server's IP address.

#### Layer 2: Data Link Layer

The Data Link layer manages access to the physical medium and packages data into frames. It adds MAC addresses (physical addresses) to ensure data is sent to the correct device on the local network. It also handles error detection within frames.

#### Layer 1: Physical Layer

The Physical layer converts the data into electrical, optical, or radio signals for transmission over the physical medium (e.g., Ethernet cables or Wi-Fi). The data travels through this medium to the server.

## 2) During encapsulation process which headers are added to the data as it traverses through the layers?

* **Application Layer:** No specific header (data is just application-specific information).
* **Presentation Layer:** (Optional) Metadata related to encryption/compression.
* **Session Layer:** No specific header.
* **Transport Layer:** TCP or UDP header.
* **Network Layer:** IP header.
* **Data Link Layer:** Frame header (e.g., Ethernet) and frame trailer.
* **Physical Layer:** No headers, only physical signaling.

## 3) Discuss the significance of PDUs in ensuring reliable data transmission across a network.

Protocol Data Units (PDUs) are essential for enabling reliable data transmission across a network. They represent the data exchanged at each layer of the OSI model and are responsible for ensuring that information is correctly transmitted, received, and interpreted.

#### 1. **Layer-Specific Data Handling**

Each layer in the OSI model has its own PDU, which is uniquely designed to handle the specific requirements and responsibilities of that layer:

* **Application Layer (Layer 7):** PDU is called `Data`. It consists of user data like emails, web pages, or files.
* **Transport Layer (Layer 4):** PDU is called a `Segment` (TCP) or `Datagram` (UDP). It ensures proper sequencing and error-checking.
* **Network Layer (Layer 3):** PDU is called a `Packet`. It includes routing information such as source and destination IP addresses.
* **Data Link Layer (Layer 2):** PDU is called a `Frame`. It includes MAC addresses and error-checking information.
* **Physical Layer (Layer 1):** PDU is a `Bit` or `Symbol`. It represents the actual transmission of raw data over a medium.

#### 2. **Segmentation and Reassembly**

PDUs allow data to be broken down into smaller, manageable pieces (segmentation) at the sender side and reassembled at the receiver side. This segmentation is crucial for:

* **Error Control:** If an error is detected in a segment, only that segment needs to be retransmitted, not the entire message.
* **Efficient Use of Bandwidth:** Smaller segments prevent the network from being congested by large data units.

## 4) What are some common use cases for Wireshark in a network environment?

Wireshark is a powerful network protocol analyzer used for capturing and analyzing network traffic. It is widely employed in various use cases across network environments.

#### 1. **Network Troubleshooting**

* **Diagnosing Connectivity Issues:** Wireshark helps identify issues like packet loss, excessive retransmissions, or misconfigurations that can lead to connectivity problems.
* **Latency Analysis:** By analyzing packet timestamps and response times, Wireshark can pinpoint sources of network latency or delays.
* **Verifying Network Configurations:** It can be used to verify proper configurations of devices, protocols, and services, ensuring that traffic flows as expected.

#### 2. **Security Analysis and Incident Response**

* **Detecting Malicious Activity:** Wireshark can identify suspicious traffic patterns, such as unusual protocols, port scanning, or abnormal data flows that may indicate malware or intrusion attempts.
* **Analyzing Attack Vectors:** During incident response, Wireshark helps dissect attack vectors and payloads, such as malicious packets, unauthorized connections, or exploit attempts.
* **Network Forensics:** By analyzing captured packets, Wireshark can help reconstruct events before, during, and after a security breach, providing insight into how an attack was carried out.

## 5) Describe the different types of transmission media commonly used at the physical layer.

Guided

a) Twisted Pair Cable

a.1) UTP

a.2) STP

b) Coaxial Cable

c) Optical Fiber Cable

Unguided

a) Radio waves

b) Microwave transmission

c) Infrared (IR)

## 6) Why is STP (Shielded twisted pair) better than UTP (Unshielded twisted pair)?

Improved Electromagnetic Interference (EMI) Protection

Higher Data Transmission Quality

Enhanced Security

Better Performance in High-Frequency Applications

## 7) Explain the concept of modulation and its significance in data transmission at the physical layer.

Modulation is a critical process in data transmission that involves altering a carrier signal to encode information. Its significance lies in its ability to efficiently use bandwidth, enable long-distance transmission, enhance resilience to interference, and adapt to different communication media. By employing various modulation techniques, data transmission systems can achieve higher data rates, better signal quality, and greater efficiency in communication.

## 8) Discuss the importance of physical layer standards in networking and interoperability.

## 9) A company wants to upgrade its network infrastructure to support higher data rates. They are using traditional copper cables. Suggest how they might achieve better transmission rate.

Upgrade to Higher Category Copper Cables (Cat6/Cat6a/Cat7 Cables)

Implement Shielded Twisted Pair (STP) Cables

Implement Fiber Optic Cabling

Optimize Network Topology and Design

## 10) Explain the difference between logical link control (LLC) and media access control (MAC) sublayers within the data link layer.

* **Primary Function:**
  * **LLC:** Manages communication between the Data Link Layer and the Network Layer, handles protocol multiplexing, and provides error and flow control.
  * **MAC:** Manages access to the physical network medium, handles addressing with MAC addresses, and controls data transmission to avoid collisions.
* **Addressing:**
  * **LLC:** Does not deal directly with hardware addresses but provides a way for different network protocols to share the same medium.
  * **MAC:** Uses unique MAC addresses to identify devices and manage data transmission on the network.
* **Error Handling:**
  * **LLC:** Provides higher-level error control and flow control, ensuring reliable data transfer between devices.
  * **MAC:** Focuses on detecting and managing collisions and errors at the level of the physical transmission medium.
* **Protocols:**
  * **LLC:** Implements standards such as IEEE 802.2 for handling protocol multiplexing and service access.
  * **MAC:** Implements standards like IEEE 802.3 (Ethernet) and IEEE 802.11 (Wi-Fi) for medium access and addressing.

In essence, the LLC sublayer provides a higher-level interface and protocol management between the Network Layer and MAC sublayer, while the MAC sublayer handles the low-level tasks of accessing the physical network medium and managing data transmission.

## 11) Discuss MAC, NIC.

**MAC (Media Access Control)** is a sublayer of the Data Link Layer (Layer 2) in the OSI model responsible for managing access to the physical transmission medium and ensuring proper communication between devices on a network.

**NIC (Network Interface Card)** is a hardware component that provides the physical interface between a computer or network device and the network. It enables the device to connect to and communicate over a network.

**Types of NICs:**

* **Wired NICs:** Connect to wired networks via Ethernet cables (e.g., Gigabit Ethernet NICs).
* **Wireless NICs:** Connect to wireless networks via Wi-Fi (e.g., 802.11n, 802.11ac NICs).
* **Fiber NICs:** Connect to fiber optic networks for high-speed, long-distance communication

In summary, the MAC sublayer handles the logic and protocols for accessing the network medium and addressing, while the NIC is the hardware that enables a device to connect to and communicate over the network, implementing MAC layer functions in physical form.

## 12) Describe what data is called in each layer while going through OSI layers?

Application Layer - Data

Presentation Layer - Data

Session Layer - Data

Transport Layer - Segment

Network Layer - Packet

Data Link Layer - Frame

Physical Layer

13\) Discuss the operation of the Ethernet protocol at the data link layer.

14\) Describe the role of logical addressing and routing tables in the network layer.

15\) Explain the difference between connection-oriented and connectionless communication at the network layer.

16\) Discuss the operation of routing protocols (RIP, OSPF, BGP, EIGRP) used at the network layer.

17\) What are the advantages and disadvantages of IPv4 and IPv6 addressing schemes at the network layer?

18\) What are remote networks?

19\) A company operates multiple branch offices connected via a wide area network (WAN). One of the branch offices experiences a network outage. Explain how the network layer can help reroute traffic to ensure uninterrupted communication.

20\) Describe the differences between TCP (Transmission Control Protocol) and UDP (User Datagram Protocol), including when each is commonly used.

21\) How does flow control work in the Transport Layer, and why is it important for efficient data transmission?

22\) Explain the concept of congestion control in the Transport Layer and how it helps optimize network performance.

23\) Describe 3-way and 4-way handshake and go over TCP flags and how they are used.

24\) Explain the client-server and peer-to-peer models in the context of the application layer.

25\) Describe DNS

26\) DNS uses both TCP and UDP for data transmission. Explain why.

27\) FTP uses ports 20 and 21. Why two ports are being used and not one?

28\) Describe the difference between active and passive network attacks.

29\) Difference DoS and DDoS?

30\) Discuss the significance of each number system in computer science and networking.

31\) What is the purpose of subnetting in networking, and how does it relate to number systems?

32\) A network engineer needs to subnet a network with a given IP address range. Describe the process of subnetting and how binary representations are used to determine subnet masks and network addresses.

33\) What are some common security risks associated with switch access?

34\) Explain the concept of port security and how it helps secure switch access.

35\) Describe the role of VLANs (Virtual Local Area Networks) in network security.

36\) How can administrators prevent VLAN hopping attacks?

37\) Discuss the importance of securing management access to switches through methods such as SSH (Secure Shell) and SNMP (Simple Network Management Protocol).

38\) Difference between SFTP and FTPS?

39\) Explain the difference between static VLANs and dynamic VLANs.

40\) How does VLAN trunking facilitate communication between VLANs across multiple switches?
