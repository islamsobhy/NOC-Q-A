# Access and Trunk Ports

## 1. Access Ports

### Purpose:
Connects a single end-device (e.g., PC, printer, non-VLAN-aware server, IP phone) to the network.

### Behavior:
* An access port is configured to belong to **only one VLAN**.
* Traffic entering an access port is *assumed* to belong to that port's configured access VLAN.
* Traffic exiting an access port is **always untagged**. End-devices do not understand VLAN tags.
* **VLAN Tagging (802.1Q):** Access ports strip any 802.1Q tags from incoming frames before forwarding them within the switch, and send untagged frames when transmitting.

## 2. Trunk Ports

### Purpose:
Connects network devices that need to carry traffic for **multiple VLANs** across a single physical link. This is typically used between switches, between a switch and a router (for inter-VLAN routing), or between a switch and a VLAN-aware server or Access Point.

### Behavior:
* A trunk port can carry traffic for **one or more VLANs**.
* It primarily uses **IEEE 802.1Q encapsulation** (VLAN tagging) to distinguish traffic belonging to different VLANs.
* When a tagged frame traverses a trunk, the 802.1Q tag (a 4-byte header) identifies the VLAN ID.
* When traffic needs to exit a trunk port for a specific VLAN, the appropriate 802.1Q tag is added.

* # Native VLAN Behavior on a Trunk Port

## 1. What is the Native VLAN?
On an IEEE 802.1Q trunk link, the **native VLAN** is a special VLAN whose traffic is transmitted **untagged** across the trunk. All other VLANs configured on the trunk are typically transmitted as tagged frames.

### Default Behavior:
* By default, Cisco switches assign VLAN 1 as the native VLAN.
* When an untagged frame is received on an 802.1Q trunk port, the receiving switch assumes that frame belongs to its configured native VLAN for that port.

## 2. Why the Native VLAN Exists
* **Legacy Device Interoperability:** Allows communication with older, non-VLAN-aware devices that may be connected to a trunk port (e.g., unmanaged hubs, some older network cards).
* **Control Plane Traffic:** Many Layer 2 control protocols (like CDP, LLDP, PAgP, LACP, VTP, DTP, and some STP BPDUs) are sent untagged over the native VLAN. This ensures these protocols function even if there are VLAN tagging issues or if VLANs are incorrectly configured.

## 3. Critical L3 NOC Engineer Considerations for Native VLANs
The native VLAN is a frequent source of both connectivity issues and security vulnerabilities.

* **Native VLAN Mismatch:** If the native VLANs configured on the two ends of a trunk link do not match, it creates a serious problem:
    * Untagged traffic sent from one side (belonging to its native VLAN) will be received by the other side and erroneously associated with *its* native VLAN. This leads to dropped packets or traffic ending up in the wrong VLAN.
    * This mismatch effectively merges two separate VLANs into one broadcast domain, posing a **significant security risk** (a form of VLAN hopping).

* **Security Risk (VLAN Hopping):**
    * An attacker can exploit the native VLAN. If they connect a device that sends untagged frames to a port configured as an access port for a VLAN that is *also* the native VLAN on an adjacent trunk, they can potentially inject traffic into the native VLAN on the trunk. This could give them access to resources on the native VLAN or even allow them to manipulate control plane protocols.
    * If VLAN 1 (the default native VLAN) is used, and it also happens to be a management VLAN or a VLAN with sensitive devices, this risk is compounded.

### Best Practices for Native VLAN:

* **Change the Default:** **NEVER use VLAN 1 as the native VLAN** in production networks. Create a dedicated, unused, and generally unroutable VLAN (e.g., VLAN 999, VLAN 4000) solely for the purpose of being the native VLAN on trunks. This isolates untagged traffic.
* **Ensure Consistency:** The native VLAN ID **MUST** match on both ends of a trunk link. Use `show interfaces [interface] trunk` to verify.
* **Do Not Assign Devices:** Do not assign any user, server, or management devices to the native VLAN itself. Keep it exclusively for untagged trunk traffic and control plane frames.
* **Prune Unused VLANs:** Use `switchport trunk allowed vlan` to explicitly define which VLANs are permitted on a trunk. While the native VLAN traffic is untagged, it's good practice to manage the allowed VLANs carefully.

# Native VLAN Mismatch

A **native VLAN mismatch** occurs when two devices connected via an 802.1Q trunk link are configured with different native VLAN IDs on their respective trunk ports. This is a common and critical misconfiguration in VLAN-aware networks, leading to both connectivity issues and security vulnerabilities.

## How a Native VLAN Mismatch Happens:
* **Configuration Error:** This is the most common cause, where an administrator manually configures different native VLAN IDs on each end of a trunk.
* **Default Settings:** One switch might retain the default native VLAN (VLAN 1, on Cisco for example), while the other is explicitly configured with a different native VLAN.
* **Auto-negotiation Issues:** While rare for native VLAN itself, underlying trunking protocol issues could indirectly lead to mismatched assumptions.

## Consequences of a Native VLAN Mismatch:

### 1. Connectivity Issues (Traffic Dropped or Misdirected)
* **Untagged Traffic:** When a device sends untagged frames over a trunk link, the receiving device will assume those frames belong to *its own* configured native VLAN. If the native VLANs don't match, the untagged traffic will be placed into the wrong VLAN on the receiving side.
* **Example:**
    * Switch A (Native VLAN 99) sends untagged frames for VLAN 99.
    * Switch B (Native VLAN 1) receives these untagged frames and assumes they belong to VLAN 1.
    * Traffic intended for devices in VLAN 99 on Switch B will be dropped or forwarded to devices in VLAN 1, leading to communication failure.
* **Control Plane Disruption:** Many Layer 2 control protocols (like CDP, LLDP, STP BPDUs, VTP) send their traffic untagged over the native VLAN. A native VLAN mismatch can cause these crucial protocol frames to be misdirected or discarded, leading to:
    * STP loops or convergence issues.
    * Inability for devices to discover neighbors.
    * VTP inconsistencies.

### 2. Security Vulnerability (VLAN Hopping)
* **Broadcast Domain Merge:** A native VLAN mismatch can effectively merge two separate VLANs into a single broadcast domain. This happens because untagged traffic from one native VLAN is received and processed as if it belongs to the other native VLAN.
* **Attacker Exploitation:** An attacker can leverage this. If an attacker connects a device that sends untagged frames to a port configured as an access port for a VLAN that is *also* the native VLAN on an adjacent trunk, they might be able to inject traffic directly into the native VLAN of the other switch. This provides a vector for "VLAN hopping" attacks, allowing the attacker to bypass segmentation and gain unauthorized access to resources in the mismatched native VLANs.
* **Sensitive Data Exposure:** If either of the mismatched native VLANs carries sensitive data or management traffic, this vulnerability becomes critical.

## How to Detect a Native VLAN Mismatch:
* **`show interfaces [interface] trunk`:** On Cisco devices, this command clearly shows the configured native VLAN for each trunk port. Mismatches will be evident by comparing the output on both ends of the trunk.
* **Console Messages/Logs:** Switches often log errors or warnings when a native VLAN mismatch is detected. Look for messages like "%CDP-4-NATIVE_VLAN_MISMATCH".
* **Connectivity Issues:** Users in a specific VLAN might experience intermittent or complete loss of connectivity across trunk links.
* **STP Instability:** Unusual Spanning Tree Protocol behavior (e.g., ports flapping, unexpected blockages, slow convergence) can sometimes point to underlying native VLAN issues affecting BPDU transmission.

## Resolution and Best Practices:

1.  **Ensure Consistency:** The native VLAN ID **MUST** match on both ends of an 802.1Q trunk link. This is the primary solution.
2.  **Change the Default:** **NEVER use VLAN 1 as the native VLAN** in production environments. Configure a dedicated, unused, and generally unroutable VLAN (e.g., VLAN 999, VLAN 4000) solely for the purpose of being the native VLAN on all trunks. This isolates untagged traffic and reduces the attack surface.
3.  **Do Not Assign User Data:** Do not assign any user, server, or management devices to the native VLAN itself. Keep it exclusively for untagged trunk traffic and control plane frames.
4.  **Prune Unnecessary VLANs:** Use `switchport trunk allowed vlan` to explicitly define which VLANs are permitted on a trunk. This helps minimize the impact of any potential misconfigurations.
5.  **Utilize DTP (Dynamic Trunking Protocol) Carefully:** While DTP can automate trunk negotiation, it can also lead to unintended trunking or native VLAN mismatches if not properly controlled. In most production environments, it's safer to explicitly configure trunk ports (`switchport mode trunk`).
