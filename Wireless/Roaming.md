# Wireless Roaming: An L3 Network Engineer's Perspective

Wireless roaming is a fundamental concept in large-scale wireless deployments, enabling client devices to maintain continuous network connectivity as they move across a coverage area.

---

### What is Wireless Roaming?

Wireless roaming is the process by which a client device (like a laptop or smartphone) seamlessly transitions its connection from one Access Point (AP) to another within the same network, or even across different networks, without losing its network connectivity or application sessions. The primary goal is to provide uninterrupted service as a user moves around a large coverage area.

---

### Why is Roaming Important?

* **Mobility:** Enables users to move freely throughout a building or campus while maintaining continuous access to network resources.
* **Application Continuity:** Ensures applications like VoIP calls, video conferences, or streaming don't drop or glitch during transitions.
* **Load Balancing:** Allows the network to distribute clients more evenly across available APs, preventing single APs from becoming overloaded.
* **Performance Optimization:** Clients can connect to the strongest available signal, improving overall performance and reducing interference.

---

### Key Concepts in Wireless Roaming

#### 1. Roaming Decision

The decision to roam is primarily made by the **client device**, not the AP or controller. Client devices constantly monitor the signal strength (RSSI) and quality (SNR) of their current AP and neighboring APs. When the signal from the current AP drops below a certain threshold, and a stronger signal from another AP is detected, the client initiates the roaming process.

#### 2. Roaming Process (Simplified)

1.  **Discovery:** The client actively or passively scans for neighboring APs broadcasting the same SSID.
2.  **Authentication:** The client authenticates with the new AP (or the controller managing it). This is where 802.11r/k/v come into play for faster roaming.
3.  **Reassociation:** The client reassociates with the new AP.
4.  **Data Path Update:** The network infrastructure (controller, switches) updates its forwarding tables to reflect the client's new location, ensuring traffic is sent to the correct AP.

---

### Types of Wireless Roaming

Wireless roaming can be broadly categorized based on whether the client's IP address changes during the transition:

#### a) Layer 2 (L2) Roaming (Intra-subnet Roaming)

* **Concept:** The client moves between APs that are on the **same Layer 2 subnet/VLAN**. In this scenario, the client's IP address remains unchanged.
* **How it Works:** This is the most common and generally the fastest type of roaming. When a client roams to a new AP within the same subnet, the wireless controller (if present) or the APs themselves facilitate the transition. The controller often updates its internal client table and uses techniques like gratuitous ARPs to inform upstream switches of the client's new location, so traffic is forwarded correctly.
* **Ideal For:** Single-building deployments, floors within a building that share the same VLAN, or any contiguous area covered by a single Layer 3 boundary.
* **Characteristics:**
    * **Fast:** No IP address change or DHCP negotiation needed.
    * **Seamless:** Minimal impact on application sessions.
    * **Requires:** APs to be on the same Layer 2 segment or managed by the same controller.

#### b) Layer 3 (L3) Roaming (Inter-subnet Roaming)

* **Concept:** The client moves between APs that are on **different Layer 2 subnets/VLANs**, meaning the client's IP address would typically need to change. However, L3 roaming mechanisms allow the client to retain its original IP address even when moving across subnets.
* **How it Works:** This is more complex than L2 roaming. It typically involves a centralized wireless controller acting as a "mobility anchor" or "home agent" for the client's original IP subnet.
    * When the client roams to an AP in a *different* subnet, the traffic for that client is encapsulated (e.g., using protocols like EoIP or CAPWAP tunneling) and sent back to the mobility anchor/home controller, which is still "owning" the client's original IP address.
    * The mobility anchor then forwards the traffic to the client's new location via the new AP.
* **Ideal For:** Large campus environments, multi-building deployments, or scenarios where different areas are on distinct IP subnets but users need seamless roaming.
* **Characteristics:**
    * **More Complex:** Requires specific controller features and tunneling protocols.
    * **Slightly Slower:** Due to encapsulation and de-encapsulation overhead, but generally imperceptible to the user.
    * **IP Address Preservation:** The key benefit is that the client's IP address remains constant, ensuring application sessions persist.

---

### Roaming Enhancements (802.11k, 802.11v, 802.11r)

These are IEEE 802.11 amendments designed to improve the roaming experience:

* **802.11k (Radio Resource Management):** Helps clients discover the best available APs by providing them with a list of neighboring APs and their radio characteristics. This reduces the client's need to scan extensively, speeding up the discovery phase.
* **802.11v (Basic Service Set (BSS) Transition Management):** Allows the network to influence client roaming decisions by suggesting better APs or even requesting a client to move to a different AP (e.g., if its current AP is overloaded). It also includes features like BSS Max Idle Period to disconnect inactive clients.
* **802.11r (Fast BSS Transition - FT):** Significantly speeds up the re-authentication process during roaming, especially with 802.1X/EAP. Instead of performing a full 802.1X exchange with each new AP, 802.11r creates a "cached" master key that can be quickly transferred between APs, drastically reducing authentication delay. This is crucial for latency-sensitive applications like VoIP.

---

### Roaming in Cloud-Managed Solutions (like Shasta.cloud)

In cloud-managed wireless solutions, the fundamental roaming concepts (L2, L3, 802.11k/v/r) still apply. However, the "controller" functionality is distributed or resides in the cloud.

* **Distributed Control Plane:** Often, the APs themselves handle much of the local roaming intelligence, with the cloud dashboard providing centralized configuration and monitoring.
* **Seamless L2 Roaming:** Typically handled very effectively as the APs communicate locally or through a lightweight tunneling mechanism to update client state.
* **L3 Roaming:** If supported, this would often involve the cloud platform orchestrating the tunneling between APs on different subnets, or utilizing a gateway/anchor device within the customer's network that the cloud platform manages. The goal from the user's perspective remains seamless IP address preservation.

Understanding roaming is vital for designing robust wireless networks, troubleshooting connectivity issues related to mobility, and optimizing performance in dynamic environments.
