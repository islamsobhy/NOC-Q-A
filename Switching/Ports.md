# Network Ports and Common Port Numbers

## 1. What are Network Ports?

In the context of computer networking, a **port** is a communication endpoint in an operating system. It's a logical construct that identifies a specific process or a type of network service. When applications or services send and receive data over a network, they do so through these ports.

* **Logical Entity:** Ports are not physical interfaces (like Ethernet ports on a switch). They are software-based identifiers.
* **Numbered:** Each port is assigned a 16-bit number, ranging from 0 to 65535.
* **Protocols:** Ports are used in conjunction with transport layer protocols like TCP (Transmission Control Protocol) and UDP (User Datagram Protocol) to direct network traffic to the correct application on a host. A combination of an IP address and a port number uniquely identifies a specific service running on a specific device.

## 2. Categories of Port Numbers

Port numbers are generally categorized as follows by the Internet Assigned Numbers Authority (IANA):

* **Well-known Ports (0-1023):** These ports are reserved for common, widely used services and applications (e.g., HTTP, FTP, SSH). They are typically used by system-level processes or programs.
* **Registered Ports (1024-49151):** These ports can be registered by software vendors for specific applications, though they are not as strictly controlled as well-known ports.
* **Dynamic/Private/Ephemeral Ports (49152-65535):** These ports are not assigned to specific services. They are typically used by client applications when initiating connections to a server. The client's operating system assigns an available port from this range for its temporary use.

## 3. Common Well-Known Ports

Here are some of the most frequently encountered well-known port numbers and the services they are associated with:

* **Port 20 (TCP): FTP Data**
    * **Service:** File Transfer Protocol (data channel). Used for the actual transfer of files.
* **Port 21 (TCP): FTP Control**
    * **Service:** File Transfer Protocol (control channel). Used for sending commands and responses between client and server.
* **Port 22 (TCP): SSH**
    * **Service:** **Secure Shell**. Provides a secure, encrypted method for remote login, command-line execution, and file transfer (SFTP/SCP). It's widely used for secure remote administration of servers and network devices.
* **Port 23 (TCP): Telnet**
    * **Service:** Telnet. Provides a command-line interface for remote communication. **Note:** Telnet is largely unencrypted and considered insecure for most modern uses, largely replaced by SSH.
* **Port 25 (TCP): SMTP**
    * **Service:** Simple Mail Transfer Protocol. Used for sending (relaying) email messages between mail servers and from email clients to a mail server.
* **Port 53 (TCP/UDP): DNS**
    * **Service:** Domain Name System. Used to translate human-readable domain names (e.g., example.com) into machine-readable IP addresses. UDP is used for standard queries, TCP for zone transfers.
* **Port 67 (UDP): DHCP Server**
    * **Service:** Dynamic Host Configuration Protocol (server side). Used by DHCP servers to listen for client requests.
* **Port 68 (UDP): DHCP Client**
    * **Service:** Dynamic Host Configuration Protocol (client side). Used by DHCP clients to send requests to servers.
* **Port 69 (UDP): TFTP**
    * **Service:** Trivial File Transfer Protocol. A simple file transfer protocol, often used for booting network devices or transferring configuration files.
* **Port 80 (TCP): HTTP**
    * **Service:** **Hypertext Transfer Protocol**. The fundamental protocol for the World Wide Web, used for transmitting web pages and other web content from servers to web browsers.
* **Port 110 (TCP): POP3**
    * **Service:** Post Office Protocol version 3. Used by email clients to retrieve email messages from a mail server.
* **Port 143 (TCP): IMAP**
    * **Service:** Internet Message Access Protocol. Another protocol for retrieving email messages, offering more advanced features than POP3 (e.g., keeping messages on the server).
* **Port 161 (UDP): SNMP**
    * **Service:** Simple Network Management Protocol. Used for network devices (routers, switches, servers) to send and receive management information.
* **Port 162 (UDP): SNMP Trap**
    * **Service:** Simple Network Management Protocol (Trap). Used by SNMP agents to send asynchronous notifications (traps) to the management station.
* **Port 3389 (TCP): RDP**
    * **Service:** Remote Desktop Protocol. Developed by Microsoft, used for providing a graphical interface to a remote computer.
* **Port 443 (TCP): HTTPS**
    * **Service:** **Hypertext Transfer Protocol Secure**. The secure version of HTTP, using SSL/TLS encryption to protect communication between a web browser and a website. Essential for secure online transactions and confidential data.
