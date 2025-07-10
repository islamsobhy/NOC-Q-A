# Etherchannel (Port-Channel / Link Aggregation) for L3 Network Engineers

## I. Core Concepts & Purpose

* **What is Etherchannel?**
    * Etherchannel is a link aggregation technology that allows you to bundle multiple physical Ethernet links into a single logical link. This logical link is often referred to as a "Port-Channel" or "LAG (Link Aggregation Group)".
    * It operates primarily at Layer 2 (Data Link Layer) for frame forwarding, but its benefits extend significantly into Layer 3 network design for routing and redundancy.
* **Key Benefits:**
    * **Increased Bandwidth:** The aggregated bandwidth of all active links in the bundle. For example, four 1 Gbps links create a 4 Gbps logical link.
    * **Redundancy/High Availability:** If one or more physical links within the bundle fail, traffic automatically shifts to the remaining active links, preventing service disruption.
    * **Load Balancing:** Traffic is distributed across the active links in the bundle, leading to more efficient utilization of available bandwidth.
    * **Simplified Spanning Tree Protocol (STP):** STP sees the entire Etherchannel as a single logical link, preventing it from blocking redundant paths within the bundle. This leads to faster convergence times and a simpler STP topology.

## II. Etherchannel Modes/Protocols

* **LACP (Link Aggregation Control Protocol)**
    * **Standard:** IEEE 802.3ad (open standard).
    * **Negotiation:** Uses LACP PDUs (Protocol Data Units) to negotiate and maintain the Etherchannel. Both sides must be LACP-capable.
    * **Modes:**
        * `active`: Actively sends LACP PDUs and tries to form an Etherchannel.
        * `passive`: Responds to LACP PDUs and forms an Etherchannel if the peer is `active`.
    * **Benefits:** Interoperability with different vendor devices, more robust negotiation, and dynamic link management.
* **PAgP (Port Aggregation Protocol)**
    * **Standard:** Cisco Proprietary.
    * **Negotiation:** Uses PAgP PDUs to negotiate and maintain the Etherchannel. Both sides must be PAgP-capable Cisco devices.
    * **Modes:**
        * `desirable`: Actively sends PAgP PDUs and and tries to form an Etherchannel.
        * `auto`: Responds to PAgP PDUs and forms an Etherchannel if the peer is `desirable`.
    * **Benefits:** Useful in Cisco-only environments.
* **Static (On Mode)**
    * **Standard:** No negotiation protocol.
    * **Configuration:** Manually configured on both ends without any negotiation.
    * **Risks:** High risk of misconfiguration and loop formation. If one side is configured for Etherchannel and the other is not, it can lead to loops as STP doesn't see a single logical link.
    * **Use Cases:** Primarily for connecting to non-Etherchannel capable devices (e.g., some servers) where you need to aggregate links without dynamic negotiation. **Generally discouraged for switch-to-switch or switch-to-router connections.**

## III. Layer 3 Etherchannel

While Etherchannel itself is a Layer 2 aggregation, it becomes crucial in Layer 3 network designs, particularly with:

* **Inter-VLAN Routing:** A Layer 3 Etherchannel (Port-Channel interface configured with an IP address) can serve as the default gateway for multiple VLANs, allowing routing between them. This is common on Layer 3 switches.
    * Instead of having a separate SVI (Switched Virtual Interface) for each VLAN on individual physical interfaces, you configure one logical Port-Channel interface with `no switchport` and assign an IP address. Then, you can create SVIs for your VLANs and perform inter-VLAN routing on the Layer 3 switch.
* **Router-on-a-Stick with Etherchannel:** If a router is performing inter-VLAN routing for multiple VLANs connected via a single Etherchannel, it would have sub-interfaces configured on the logical Port-Channel interface.
    * `interface Port-channelX.Y` (where Y is the VLAN ID), `encapsulation dot1Q Y`, `ip address A.B.C.D E.F.G.H`.
* **Redundant Routing Paths:** Etherchannel provides a single logical path for routing protocols. If one physical link in the bundle fails, the routing protocol (e.g., OSPF, EIGRP, BGP) continues to see the Etherchannel as up, avoiding routing reconvergence delays. This improves routing stability.

## IV. Configuration Considerations for L3 Engineers

* **Consistency is Key:** All physical interfaces participating in an Etherchannel must have identical configurations:
    * Speed
    * Duplex
    * VLAN (if Layer 2 Etherchannel, either all access ports in the same VLAN or all trunk ports with the same allowed VLANs and native VLAN)
    * Allowed VLANs and Native VLAN (for trunking Etherchannels)
    * Port Security settings
    * STP settings (if applicable)
* **Load Balancing Algorithms:**
    * Etherchannel load balancing is **per-flow**, not per-packet. This means all packets belonging to a single flow (e.g., same source/destination IP/MAC/port) will traverse the *same* physical link within the bundle.
    * Common load balancing methods (configured globally on the switch):
        * `src-mac` (Source MAC address)
        * `dst-mac` (Destination MAC address)
        * `src-dst-mac` (Source and Destination MAC address)
        * `src-ip` (Source IP address)
        * `dst-ip` (Destination IP address)
        * `src-dst-ip` (Source and Destination IP address)
        * `src-port` (Source TCP/UDP port)
        * `dst-port` (Destination TCP/UDP port)
        * `src-dst-port` (Source and Destination TCP/UDP port)
    * **Interview Tip:** Be prepared to explain how choosing the right load balancing method impacts traffic distribution based on your network's traffic patterns (e.g., if you have many flows between a few hosts, `src-dst-ip` might be better than `src-ip` to distribute traffic).
* **Number of Links:** Typically, 2 to 8 active links are recommended. Some devices support up to 16, with 8 active and 8 in standby.
* **Port-Channel Interface Creation:**
    * You create a logical `interface Port-channelX` where X is the channel group number.
    * Then, you assign the physical interfaces to this channel group.

## V. Command-Line Interface (Cisco IOS Examples)

### Layer 2 Etherchannel Example:

```cisco
// On Switch A
interface range GigabitEthernet0/1 - 2
  switchport mode trunk
  channel-group 1 mode active // or desirable for PAgP, or on for static

interface Port-channel1
  switchport mode trunk
  // Other trunking configurations (e.g., switchport trunk allowed vlan ...)

// On Switch B (matching configuration)
interface range GigabitEthernet0/1 - 2
  switchport mode trunk
  channel-group 1 mode active

interface Port-channel1
  switchport mode trunk
