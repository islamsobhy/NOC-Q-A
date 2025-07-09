# Wireless User Authentication Types


---

## 1. Open Authentication

* **Concept:** This is the most basic form of authentication. The client sends an authentication request, and the Access Point (AP) accepts it without requiring any credentials. There's no password or passphrase involved at the wireless layer for association.

* **Use Cases:**
    * Public hotspots (e.g., airports, cafes)
    * Guest networks (where a captive portal handles the actual "authentication" at a higher layer)
    * Unencrypted networks (highly discouraged for anything but truly public, non-sensitive data).

* **Security Implications:**
    * Offers no security at the wireless link layer itself. Any device can connect.
    * If used, it almost always relies on higher-layer security like a captive portal or VPN to protect data and control access.

---

## 2. Pre-Shared Key (PSK) / WPA2-Personal / WPA3-Personal

* **Concept:** A single, common passphrase (the Pre-Shared Key or PSK) is configured on both the Access Point (AP) and all client devices. When a client tries to connect, it performs a four-way handshake with the AP to prove it knows the PSK without actually transmitting the key itself. This handshake generates unique encryption keys for the session.

* **Use Cases:**
    * Home networks
    * Small office/home office (SOHO) environments
    * Small businesses where a full RADIUS infrastructure isn't practical.

* **Security Implications:**
    * Provides encryption for data in transit.
    * If the PSK is compromised, all users on the network are affected, and changing it is disruptive.
    * **WPA3-Personal** (using Simultaneous Authentication of Equals - SAE) significantly enhances security over WPA2-Personal by making offline dictionary attacks much harder and providing forward secrecy.

---

## 3. Dynamic Pre-Shared Key (DPSK)

* **Concept:** This is an evolution of the traditional PSK model, primarily found in some vendor solutions (like Ruckus, Huawei). Instead of a single PSK for all users, **each user or device gets a unique, dynamically generated PSK**. This key is often tied to a specific MAC address or user account on an authentication server (like a RADIUS server or the WLAN controller's internal database).

* **How it works:**
    * An administrator or the system generates unique PSKs for individual users or devices.
    * These unique PSKs are provisioned to the client devices.
    * When a client connects, it uses its unique PSK, and the network validates it against the stored unique key.
    * This provides the simplicity of PSK (no 802.1X supplicant needed on the client) with enhanced security and manageability, as compromised keys can be revoked individually without affecting other users.

* **Use Cases:**
    * Environments where 802.1X is too complex to deploy on all devices (e.g., IoT devices, legacy devices) but a higher level of security than a single shared PSK is desired.
    * Common in educational institutions, dorms, or specific guest access scenarios.

* **Security Implications:**
    * Significantly improves security over static PSK by limiting the impact of a compromised key.
    * Enables individual user/device accountability.
    * Can also support per-user VLAN assignment.

---

## 4. Portal Authentication (Captive Portal)

* **Concept:** Also known as Web Authentication. Users initially connect to an "open" or lightly secured Wi-Fi network. When they open a web browser, their traffic is redirected to a special web page (the "captive portal") where they must provide credentials (username/password, guest pass code, email address, social media login, or click "accept terms"). Only after successful interaction with the portal is full network access granted.

* **How it works:**
    1.  Client associates with the Wi-Fi SSID (often open or WPA2/3-Personal with a simple PSK).
    2.  The network, typically via a firewall or a specialized gateway, intercepts HTTP/HTTPS traffic from unauthenticated clients.
    3.  DNS requests for unauthenticated clients might be hijacked to resolve to the captive portal's IP address.
    4.  The client's browser is redirected to the captive portal webpage.
    5.  User provides credentials/information on the portal.
    6.  The portal communicates with an authentication server (e.g., RADIUS, internal database) to validate credentials.
    7.  Upon success, the network device (AP, controller, gateway) removes the client from the "walled garden" (restricted access) and grants full network access.

* **Use Cases:**
    * Guest Wi-Fi
    * Public hotspots (hotels, airports, cafes)
    * Educational institutions, retail.
    * Excellent for onboarding temporary users or for compliance (AUP acceptance).

* **Security Implications:**
    * The initial connection to the Wi-Fi might be open or weakly secured. The actual authentication and access control occur at Layer 3/4.
    * Data may or may not be encrypted over the air, depending on the underlying Wi-Fi security (e.g., Open + Portal vs. PSK + Portal).

---

## 5. 802.1X / EAP (Enterprise Authentication) - The Gold Standard

* **Concept:** This is the robust, industry-standard authentication method for enterprise networks (WPA2-Enterprise, WPA3-Enterprise). It provides port-based network access control. Instead of a single shared key, each user or device authenticates individually using unique credentials (username/password, digital certificates).

* **Components:**
    * **Supplicant:** The client device (e.g., laptop, smartphone) running 802.1X client software.
    * **Authenticator:** The Access Point (AP) or wireless controller, which acts as a middleman.
    * **Authentication Server:** Typically a RADIUS server (e.g., Cisco ISE, Aruba ClearPass, Microsoft NPS) that holds the user/device credentials and policies.

* **How it works (simplified):**
    1.  Client associates with the AP (Layer 2 connectivity).
    2.  The AP puts the client into a restricted state, only allowing EAP (Extensible Authentication Protocol) traffic to the RADIUS server.
    3.  The Supplicant and Authentication Server exchange authentication messages via EAP (tunneled within 802.1X frames). Common EAP types include:
        * **PEAP (Protected EAP):** Uses a TLS tunnel to protect inner authentication methods (e.g., MS-CHAPv2 for username/password). Widely used.
        * **EAP-TLS (Transport Layer Security):** Uses digital certificates for both the client and the server, offering the strongest security. No passwords are exchanged.
        * **EAP-TTLS (Tunneled TLS):** Similar to PEAP, but uses a TLS tunnel to protect various inner authentication methods.
        * **EAP-FAST (Flexible Authentication via Secure Tunneling):** Cisco proprietary, uses PACs (Protected Access Credentials) for authentication.
    4.  If authentication is successful, the RADIUS server sends an "Access-Accept" message to the AP, often including authorization attributes like VLAN assignment, QoS policies, or ACLs.
    5.  The AP then grants full network access to the client.

* **Use Cases:**
    * Corporate networks, universities, government agencies – any environment requiring strong security, individual accountability, and dynamic policy assignment.

* **Security Implications:**
    * Highly secure.
    * Eliminates shared keys, enables per-user/device policies.
    * Supports multi-factor authentication.
    * Can dynamically assign users to specific VLANs based on their identity or group.

---

## Other Authentication Types/Concepts:

* **MAC Authentication Bypass (MAB):** Often used in conjunction with 802.1X. For devices that don't support 802.1X (e.g., old printers, IP phones, some IoT devices), the network can authenticate them based on their MAC address against a database on the RADIUS server. It's less secure as MAC addresses can be spoofed.

* **Wired Equivalent Privacy (WEP):** An older, highly insecure standard. **Never use this.** It's easily cracked and provides almost no real security.

* **WPA (Wi-Fi Protected Access):** An interim standard released before WPA2 to address WEP's flaws. It uses TKIP encryption. While better than WEP, it's considered outdated and vulnerable.

* **Guest Pass (Voucher-based):** A variation of portal authentication where a pre-generated, often time-limited, alphanumeric code is used as a credential on a captive portal.

* **Social Login:** Another captive portal variation where users authenticate using their existing social media accounts (e.g., Google, Facebook).

---
