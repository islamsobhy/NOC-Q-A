# VLANs and Native VLANs Overview 

## 1. Virtual Local Area Networks (VLANs)

### What are VLANs?
A Virtual Local Area Network (VLAN) is a logical grouping of network devices (e.g., computers, servers, printers) that appear to be on the same Local Area Network (LAN), regardless of their physical location. VLANs achieve this by segmenting a single physical switch into multiple logical broadcast domains.

### Why use VLANs?
For an L3 NOC Engineer, VLANs are fundamental for:

* **Broadcast Domain Segmentation:** Each VLAN is a separate broadcast domain. This limits the propagation of broadcast traffic, reducing network congestion and improving performance, especially in large flat networks.
* **Security:** By isolating devices into different VLANs, you prevent unauthorized access to sensitive resources. For instance, guest users can be in a separate VLAN from corporate servers.
* **Flexibility and Scalability:** VLANs allow for flexible network design. Devices can be moved within the network without re-cabling, just by changing their port's VLAN assignment. New departments or services can be easily added by creating new VLANs.
* **Traffic Management/QoS:** VLANs enable granular control over traffic. You can apply Quality of Service (QoS) policies or access control lists (ACLs) to specific VLANs to prioritize critical applications or restrict traffic flow.
* **Simplified Management:** While initial setup might seem complex, VLANs can simplify ongoing management by grouping devices logically rather than physically.

### Types of VLAN Ports:

1.  **Access Ports:**
    * **Purpose:** Connects to an end-device (e.g., PC, server, IP phone, printer) that is unaware of VLAN tagging.
    * **Behavior:** Carries traffic for only a **single VLAN**. When traffic leaves an access port, it is *untagged*. When traffic arrives at an access port, it is assumed to belong to the VLAN configured on that port.
    * **Configuration:** `switchport mode access` and `switchport access vlan [VLAN ID]`

2.  **Trunk Ports:**
    * **Purpose:** Connects to other network devices like switches, routers, or servers that are VLAN-aware.
    * **Behavior:** Carries traffic for **multiple VLANs** across a single physical link. Traffic belonging to different VLANs is distinguished by a VLAN tag (most commonly 802.1Q).
    * **Configuration:** `switchport mode trunk` and `switchport trunk encapsulation dot1q` (if explicit, though often automatic on newer devices). You can also control which VLANs are allowed on a trunk: `switchport trunk allowed vlan [VLAN IDs]`.

### VLAN Tagging (802.1Q):
The industry standard for VLAN tagging is **IEEE 802.1Q**. When traffic for multiple VLANs traverses a trunk link, 802.1Q inserts a 4-byte tag into the Ethernet frame header. This tag contains:
* **TPID (Tag Protocol Identifier):** A 2-byte field, always `0x8100`, identifying the frame as an 802.1Q tagged frame.
* **TCI (Tag Control Information):** A 2-byte field containing:
    * **Priority (3 bits):** CoS (Class of Service) for QoS.
    * **CFI (Canonical Format Indicator) / DEI (Drop Eligibility Indicator) (1 bit):** Used for compatibility between Ethernet and Token Ring (CFI) or for explicitly indicating frames that can be dropped during congestion (DEI).
    * **VLAN ID (12 bits):** The crucial field, identifying which VLAN the frame belongs to. This allows for 4096 possible VLAN IDs (0-4095).

## 2. Native VLAN

### What is the Native VLAN?
The native VLAN on an 802.1Q trunk link is a special VLAN whose traffic is **untagged** when traversing the trunk. All other VLANs on the trunk are typically tagged.

### How it works:
* When a frame belonging to the native VLAN is sent over an 802.1Q trunk, it is *not* tagged with an 802.1Q header.
* When an untagged frame is received on an 802.1Q trunk port, the receiving switch associates that frame with the native VLAN configured on that port.

### Default Native VLAN:
By default, VLAN 1 is the native VLAN on Cisco switches. It is also the default management VLAN.

### Why is the Native VLAN important for L3 NOC Engineers?

1.  **Interoperability with Legacy Devices:** Some older or simpler devices (e.g., unmanaged switches, some IP phones, or servers that are not VLAN-aware) might send or expect untagged traffic. The native VLAN allows these devices to communicate over a trunk link without requiring them to understand 802.1Q tagging.
2.  **Control Plane Traffic:** Certain control plane protocols (e.g., CDP, LLDP, PAgP, LACP, VTP, DTP, and some Spanning Tree Protocol BPDUs) are often sent untagged on the native VLAN. This ensures these protocols can operate even if VLAN tagging issues are present.
3.  **Troubleshooting:** Mismatched native VLANs between two ends of a trunk link are a common cause of connectivity issues. If the native VLANs don't match, untagged traffic sent by one switch will be placed into the *wrong* VLAN by the other switch, leading to communication failures.

### Security Concerns with Native VLAN:

The native VLAN is a significant security consideration for L3 NOC Engineers:

* **VLAN Hopping Attacks:** If a malicious actor connects an untagged device to a port configured as an access port for a specific VLAN, and that VLAN also happens to be the native VLAN on an adjacent trunk, the attacker might be able to inject traffic into other VLANs on the trunk.
* **Unintended Access:** Since untagged traffic is automatically associated with the native VLAN, if an attacker can send untagged traffic onto a trunk, they gain access to the native VLAN's resources.
* **Control Plane Exposure:** As control plane traffic often uses the native VLAN, compromising the native VLAN can potentially expose or disrupt network management.

### Best Practices for Native VLAN (L3 NOC Engineer):

1.  **Change the Default Native VLAN:** **Never use VLAN 1 as the native VLAN** on production networks. Create a dedicated, unused, and otherwise empty VLAN (e.g., VLAN 999 or VLAN 4000) and assign it as the native VLAN. This prevents unauthorized access to the default VLAN 1.
2.  **Do Not Use the Native VLAN for User Data:** Do not assign any user or server devices to the native VLAN. Keep it strictly for untagged control plane traffic and interoperability.
3.  **Ensure Native VLAN Consistency:** The native VLAN ID **must match** on both ends of an 802.1Q trunk link. A mismatch will cause connectivity issues for any untagged traffic and can create a security vulnerability by effectively merging broadcast domains.
4.  **Remove Unnecessary VLANs from Trunk:** Use `switchport trunk allowed vlan` to explicitly define which VLANs are permitted on a trunk, and *do not* include the native VLAN in the "allowed" list if possible (depending on device behavior, some devices will still consider it allowed even if not explicitly listed, as it's untagged). This helps prune unnecessary broadcast domains.
5.  **Use `switchport trunk native vlan [VLAN ID]`:** Explicitly configure the native VLAN on each trunk port.

## Configuration Examples (Cisco IOS):

**Configuring a Trunk Port with Native VLAN:**

```cisco
interface GigabitEthernet0/1
 switchport mode trunk
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 999  ! Configure a non-default native VLAN
 switchport trunk allowed vlan 10,20,30,999 ! Allow specific VLANs, including native
end
