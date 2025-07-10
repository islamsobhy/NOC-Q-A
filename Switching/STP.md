# Spanning Tree Protocol (STP)

## 1. Purpose of STP

Spanning Tree Protocol (STP) is a network protocol that ensures a loop-free logical topology for Ethernet networks. Its primary goal is to prevent broadcast storms, MAC address table instability, and multiple frame transmissions that can occur due to redundant paths between network devices (like switches).

### Key Problems Solved by STP:
* **Broadcast Storms:** In a looped topology, a broadcast frame would circulate endlessly, consuming all available bandwidth and bringing the network down. STP blocks redundant paths to prevent this.
* **MAC Address Table Instability:** Switches learn MAC addresses by observing incoming frames. In a loop, a frame from a single source could arrive on multiple ports, causing the switch's MAC address table to constantly update and become unstable, leading to incorrect forwarding.
* **Multiple Frame Transmissions:** A single frame could be duplicated and delivered multiple times to the destination, leading to retransmissions and application-level issues.

## 2. How STP Works (IEEE 802.1D)

STP achieves a loop-free topology by forcing redundant data paths into a blocking state. It does this through a series of elections and calculations:

### 2.1. Bridge ID (BID)
Each switch (or "bridge" in STP terminology) has a unique Bridge ID, which is an 8-byte value consisting of:
* **Bridge Priority (2 bytes):** A configurable value (default 32768, configurable in increments of 4096).
* **MAC Address (6 bytes):** The base MAC address of the switch.

The BID is crucial for the Root Bridge election.

### 2.2. Root Bridge Election
* The first step in STP convergence is the election of a **Root Bridge**.
* The switch with the **lowest Bridge ID** becomes the Root Bridge.
* If priorities are equal, the switch with the numerically lowest MAC address wins the election.
* The Root Bridge acts as the central point of reference for all STP calculations in the broadcast domain.

### 2.3. Path Cost
* STP assigns a **path cost** to each link based on its bandwidth. Lower cost is preferred.
* Common STP path costs (IEEE 802.1D standard):
    * 10 Mbps: 100
    * 100 Mbps: 19
    * 1 Gbps: 4
    * 10 Gbps: 2
* These costs are used in electing Root Ports and Designated Ports.

### 2.4. Port Roles
Once the Root Bridge is elected, all other switches calculate the best path back to the Root Bridge. This determines the role of each port:

* **Root Port (RP):**
    * On non-Root Bridge switches, the Root Port is the single port that provides the **lowest cost path** back to the Root Bridge.
    * There is only one Root Port per non-Root Bridge switch.
    * All Root Ports are in a **forwarding state**.

* **Designated Port (DP):**
    * For each LAN segment (link between two switches), there must be one and only one Designated Port.
    * The Designated Port is the port on that segment that offers the **lowest cost path** to the Root Bridge for that segment.
    * If costs are equal, the switch with the lower BID becomes the Designated Port for its end of the segment. If BIDs are equal (e.g., in a single switch with multiple ports to a segment via a hub, though rare), the port with the lower Port ID (port number) wins.
    * All Designated Ports are in a **forwarding state**.

* **Blocked Port (BP) / Non-Designated Port:**
    * Any port that is *not* a Root Port or a Designated Port is a Blocked Port.
    * These ports are placed in a **blocking state** to prevent loops. They still listen for BPDUs but do not forward user data frames.
    * If a primary path fails, a blocked port can transition to a forwarding state.

### 2.5. Bridge Protocol Data Units (BPDUs)
* STP-enabled switches exchange **Bridge Protocol Data Units (BPDUs)** to communicate STP information (like Root ID, Root Path Cost, Sender BID, Port ID).
* BPDUs are sent out of all active ports at regular intervals (default 2 seconds - Hello Time).
* BPDUs are multicast frames (destination MAC 01:80:C2:00:00:00).

## 3. STP Port States

STP ports transition through different states during convergence:

1.  **Blocking:** (Initial state or blocking redundant paths)
    * Receives BPDUs.
    * Does NOT send BPDUs (unless it's a Designated Port, it still generates BPDUs for its segment even if it receives better BPDUs).
    * Does NOT forward data frames.
    * Does NOT learn MAC addresses.
    * *Transition to Listening (20 seconds - Max Age)* if determined to be a Root or Designated Port.

2.  **Listening:** (Determining port role and active topology)
    * Receives BPDUs.
    * Sends BPDUs.
    * Does NOT forward data frames.
    * Does NOT learn MAC addresses.
    * *Transition to Learning (15 seconds - Forward Delay)*.

3.  **Learning:** (Populating MAC address table)
    * Receives BPDUs.
    * Sends BPDUs.
    * Does NOT forward data frames.
    * **Learns MAC addresses.**
    * *Transition to Forwarding (15 seconds - Forward Delay)*.

4.  **Forwarding:** (Fully operational)
    * Receives BPDUs.
    * Sends BPDUs.
    * Forwards data frames.
    * Learns MAC addresses.

5.  **Disabled:** (Administratively down or faulty)
    * Does not participate in STP.
    * Does not forward data.

## 4. STP Timers

* **Hello Time (2 seconds default):** How often BPDUs are sent by the Root Bridge (and propagated by Designated Ports).
* **Forward Delay (15 seconds default):** The time spent in the Listening and Learning states. This delay ensures that MAC addresses are learned and the network topology stabilizes before data forwarding begins, preventing temporary loops.
* **Max Age (20 seconds default):** How long a switch stores a BPDU. If a switch doesn't hear a BPDU from the Root Bridge (or its Designated Port) within Max Age, it assumes a failure and begins the STP recalculation process.

## 5. STP Enhancements and Variations

* **PortFast (Cisco):** Immediately brings an access port to the forwarding state, bypassing Listening/Learning, for end devices that don't participate in STP. Prevents initial connectivity delays.
* **BPDU Guard (Cisco):** Shuts down a PortFast-enabled port if it receives a BPDU, preventing unauthorized switches from affecting the STP topology.
* **BPDU Filter (Cisco):** Prevents BPDUs from being sent or received on an interface. Use with extreme caution as it breaks STP on that link.
* **UplinkFast/BackboneFast (Cisco):** Proprietary enhancements for faster convergence in specific failure scenarios.
* **Rapid Spanning Tree Protocol (RSTP - IEEE 802.1w):** A faster converging version of STP. It reduces convergence time from 30-50 seconds (802.1D) to often less than a second by:
    * Introducing new port states (Discarding, Learning, Forwarding).
    * Using "proposal/agreement" handshakes between switches.
    * Defining new port types (Edge Port, Point-to-Point, Shared).
* **Multiple Spanning Tree Protocol (MSTP - IEEE 802.1s):** Allows multiple STP instances to run, mapping different VLANs to different STP instances. This optimizes resource utilization and load balancing by allowing different VLANs to use different root bridges and paths.
* **Per-VLAN Spanning Tree Plus (PVST+/Rapid PVST+ - Cisco):** Cisco proprietary versions that run a separate STP instance for each VLAN, allowing for per-VLAN load balancing and finer control.
