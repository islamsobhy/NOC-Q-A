# AAA (Authentication, Authorization, Accounting) and RADIUS

AAA and RADIUS are fundamental concepts in network security and access management. AAA is a conceptual framework, while RADIUS is a widely used protocol that implements this framework.

---

### 1. AAA (Authentication, Authorization, and Accounting)

AAA is a security framework that controls access to network resources, enforces policies, and tracks usage. It's not a protocol itself, but a conceptual model composed of three distinct services:

* **Authentication:**
    * **What it does:** Verifies the identity of a user or device trying to access a network resource. It answers the question: "Who are you?"
    * **How it works:** Typically involves checking credentials (username/password, certificate, token) against a database or directory service.
    * **Examples:** Logging into a Wi-Fi network, a VPN, a router, or an operating system.

* **Authorization:**
    * **What it does:** Determines what an authenticated user or device is *allowed* to do on the network. It answers the question: "What are you allowed to do?"
    * **How it works:** Based on the user's identity, group membership, or assigned roles, the system grants specific permissions or restrictions (e.g., access to certain VLANs, QoS policies, firewall rules, command-line privileges).
    * **Examples:** A guest user is only allowed internet access, while an employee can access internal servers. An administrator can execute all commands, but a junior technician can only view configurations.

* **Accounting:**
    * **What it does:** Tracks and records network resource usage by authenticated and authorized users/devices. It answers the question: "What did you do, and for how long?"
    * **How it works:** Collects data such as login/logout times, duration of connection, amount of data transferred, services accessed, and commands executed.
    * **Examples:** Tracking VPN session duration, logging administrative command history, monitoring data consumption for billing or auditing purposes. This data is crucial for security audits, billing, capacity planning, and troubleshooting.

---

## How RADIUS Relates to AAA

**RADIUS (Remote Authentication Dial-In User Service)** is a widely used networking protocol that implements the AAA framework. It operates on a client-server model:

* **RADIUS Client:**
    * This is typically a Network Access Server (NAS), such as a Wi-Fi access point, a VPN concentrator, a router, or a switch.
    * When a user tries to access the network through the NAS, the NAS acts as a RADIUS client.

* **RADIUS Server:**
    * This is a centralized server that holds user credentials and access policies.
    * It performs the actual AAA functions.

### Simplified Flow of RADIUS with AAA:

1.  **Authentication Request:**
    * A user attempts to connect to the network via a RADIUS client (e.g., enters username and password on a Wi-Fi login page).

2.  **Access-Request (Authentication/Authorization):**
    * The RADIUS client sends an `Access-Request` packet to the RADIUS server. This packet contains the user's credentials (username, password, etc.) and potentially information about the client (e.g., NAS IP address, port).

3.  **Authentication and Authorization by RADIUS Server:**
    * The RADIUS server receives the `Access-Request`.
    * It **authenticates** the user by comparing the provided credentials against its internal database or an external directory (like Active Directory or LDAP).
    * If authentication is successful, the RADIUS server then checks its policies to **authorize** what the user is allowed to do.

4.  **Access-Accept/Reject:**
    * If authentication and authorization are successful, the RADIUS server sends an `Access-Accept` packet back to the RADIUS client. This packet can also include authorization attributes (e.g., VLAN assignment, access control lists, session timeouts).
    * If authentication or authorization fails, the RADIUS server sends an `Access-Reject` packet.

5.  **Network Access (Client):**
    * The RADIUS client (NAS) then allows or denies the user access to the network based on the `Access-Accept` or `Access-Reject` message.

6.  **Accounting (Session Start/Update/Stop):**
    * Once the user is connected, the RADIUS client sends `Accounting-Request` packets (Start, Interim-Update, Stop) to the RADIUS server to record session details, resource usage, and other activities. The RADIUS server acknowledges these with `Accounting-Response` packets.

### Key Characteristics of RADIUS:

* **Centralized Management:** Allows administrators to manage user accounts and access policies from a central location, rather than configuring each network device individually.
* **UDP-based:** RADIUS uses UDP (User Datagram Protocol), which is connectionless.
* **Authentication and Authorization Combined (historically):** Historically, RADIUS combines authentication and authorization into the `Access-Request` and `Access-Accept` packets. While it still fundamentally works this way, more granular authorization can be passed through attributes.
* **Security:** Passwords within RADIUS packets are typically encrypted (using MD5 hash), but the rest of the packet might not be. This is a consideration for highly secure environments, sometimes leading to alternatives like TACACS+ or Diameter which encrypt the entire packet.

In essence, AAA is the overarching security concept, and RADIUS is a widely adopted protocol that provides the practical implementation of that concept in many network environments.
