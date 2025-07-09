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

### 2. RADIUS (Remote Authentication Dial-In User Service)

RADIUS is a widely used **networking protocol** that provides centralized AAA services. It operates at the Application Layer and typically uses UDP (ports 1812 for authentication/authorization and 1813 for accounting).

* **Role of RADIUS:** It acts as an intermediary between a Network Access Server (NAS) – which could be a Wi-Fi Access Point/Controller, VPN concentrator, network switch, or router – and a central database containing user credentials and policies.

* **Components of a RADIUS system:**
    1.  **Client (NAS):** The device that users connect to (e.g., Wi-Fi AP, VPN concentrator, switch). It forwards authentication and accounting requests to the RADIUS server.
    2.  **RADIUS Server:** The central server that performs the AAA functions. It holds user databases (or integrates with external directories like Active Directory, LDAP), policies, and logs accounting data.
    3.  **User/Supplicant:** The end-user or client device trying to gain access.

* **How RADIUS Implements AAA (Example with Wi-Fi Authentication):**

    1.  **Authentication:**
        * A Wi-Fi client (Supplicant) attempts to connect to an Access Point (NAS).
        * The AP (RADIUS Client) receives the authentication request (e.g., EAP message containing username/password).
        * The AP forwards an **Access-Request** message (containing username, password, calling station ID, etc.) to the RADIUS Server.
        * The RADIUS Server verifies the credentials against its user database.
        * If valid, the RADIUS Server sends an **Access-Accept** message back to the AP. If invalid, it sends an **Access-Reject**.

    2.  **Authorization:**
        * Along with the Access-Accept,
