# SSIDs (Service Set Identifiers): Understanding Wi-Fi Network Names

An **SSID (Service Set Identifier)** is a fundamental concept in Wi-Fi networks, serving as the name of a wireless network. Understanding SSIDs is crucial for both network users and administrators.

---

### What is an SSID?

An **SSID** is essentially the **name of a Wi-Fi network**. It's a sequence of up to 32 alphanumeric characters that serves as a unique identifier for a wireless local area network (WLAN). When you look for available Wi-Fi networks on your device (laptop, phone, tablet), the names you see in the list are the SSIDs.

---

### Purpose of an SSID

The primary purposes of an SSID are:

1.  **Identification:** It allows users to distinguish one Wi-Fi network from another, especially in areas where multiple networks might be present.
2.  **Association:** A client device must know and select the correct SSID to associate with a particular Wi-Fi network. It's the first step in connecting to a WLAN.
3.  **Logical Grouping:** It groups all the wireless devices (Access Points and clients) that belong to the same logical Wi-Fi network.

---

### How SSIDs are Broadcast

Access Points (APs) typically broadcast their SSIDs using special management frames called **beacons**. Beacons are sent out periodically (e.g., every 100 milliseconds) and contain essential information about the wireless network, including:

* The SSID
* Supported data rates
* Security type (WPA2-PSK, WPA2-Enterprise, etc.)
* Channel information
* Timestamp for synchronization

Client devices passively listen for these beacon frames to discover available Wi-Fi networks.

---

### Concepts Related to SSIDs

#### 1. Multiple SSIDs (Multi-SSID)

Most enterprise-grade Access Points and controllers can broadcast multiple SSIDs simultaneously from a single physical AP.

* **Why use multiple SSIDs?**
    * **Traffic Segmentation:** Each SSID can be mapped to a different VLAN (Virtual Local Area Network). This allows you to logically separate different types of users or traffic. For example:
        * `Corporate-SSID` -> VLAN 10 (Employees)
        * `Guest-SSID` -> VLAN 20 (Guest Internet Access)
        * `IoT-SSID` -> VLAN 30 (Smart devices, cameras)
    * **Different Security Policies:** Each SSID can have its own unique authentication and encryption settings (e.g., `Corporate-SSID` uses WPA2-Enterprise with 802.1X, while `Guest-SSID` uses a Captive Portal).
    * **QoS (Quality of Service) Policies:** Different QoS levels can be applied per SSID.

* **Impact on Performance:** While convenient, each additional SSID adds overhead to the wireless network because each SSID requires its own set of beacon frames. Too many SSIDs can consume valuable airtime and reduce overall throughput, especially in dense environments. Best practice often suggests limiting the number of active SSIDs to 2-4 per radio.

#### 2. Hidden SSIDs (Non-Broadcast SSIDs)

A "hidden" or "non-broadcast" SSID is an SSID that the Access Point does not include in its regular beacon frames.

* **How to connect:** To connect to a hidden SSID, the client device must manually know and enter the exact SSID name. It then sends "probe request" frames with the SSID name, and the AP responds if it recognizes the name.
* **Security (Misconception):** Hiding an SSID is **not a security feature**. It provides a very minor and easily bypassed obscurity. Tools (like Wi-Fi analyzers or sniffers) can still detect hidden SSIDs by observing probe requests and other frames exchanged between clients and the AP. Relying on a hidden SSID for security is known as "security through obscurity"
