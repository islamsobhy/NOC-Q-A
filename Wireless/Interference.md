# Wireless Interference: Causes, Impact, and Mitigation

Wireless interference is a critical factor affecting Wi-Fi network performance. Understanding its types, sources, and how to mitigate it is essential for any wireless engineer.

---

### What is Wireless Interference?

Wireless interference refers to any unwanted signal that disrupts the normal operation of a Wi-Fi network. It occurs when other radio frequency (RF) signals compete with or overlap the Wi-Fi signal, making it difficult for wireless devices to transmit and receive data reliably. This leads to symptoms like slow speeds, frequent disconnections, high retransmission rates, and reduced range.

---

### Types of Wireless Interference

Interference can generally be categorized into two main types:

1.  **Co-Channel Interference (CCI):**
    * **Concept:** Occurs when multiple Wi-Fi Access Points (APs) operating on the **same channel** have overlapping coverage areas.
    * **How it happens:** Even if APs are configured to transmit at low power, their signals can bleed into adjacent cells. Clients in the overlap zone hear multiple APs on the same channel, leading to increased contention (more devices trying to talk on the same channel at the same time).
    * **Impact:** Reduces overall throughput, increases latency, and can lead to clients "sticking" to a distant AP because they hear it on the same channel as a closer AP.
    * **Mitigation:** Careful channel planning, proper AP placement, power adjustments, and using 5 GHz (due to more non-overlapping channels) are key.

2.  **Adjacent Channel Interference (ACI):**
    * **Concept:** Occurs when Wi-Fi APs operate on **overlapping (but not identical) channels** in close proximity. This is particularly prevalent in the 2.4 GHz band where non-overlapping channels are scarce (e.g., if one AP uses channel 1 and another uses channel 2 or 3).
    * **How it happens:** The "skirts" of the radio signal extend beyond the primary channel width. If channels are too close, these overlapping frequencies interfere with each other.
    * **Impact:** Can be even more detrimental than CCI, as devices on adjacent channels may not properly defer to each other (as they would on the same channel using CSMA/CA). This leads to increased frame errors, retransmissions, and significant performance degradation.
    * **Mitigation:** Strict adherence to non-overlapping channels (1, 6, 11 in 2.4 GHz), proper AP spacing, and power control. This is why using only 1, 6, and 11 is paramount in 2.4 GHz.

---

### Sources of Wireless Interference

Interference sources can be broadly classified into:

1.  **Wi-Fi Interference (Co-Channel and Adjacent Channel):**
    * Your own APs (due to poor design or RRM settings).
    * Neighboring Wi-Fi networks (from other businesses, apartments, homes).
    * Rogue APs.

2.  **Non-Wi-Fi Interference (Non-802.11 Interference):** These are devices that emit RF energy in the 2.4 GHz or 5 GHz bands but do not adhere to the 802.11 Wi-Fi standard. They don't perform "listen before talk" (CSMA/CA) and simply broadcast, effectively jamming the Wi-Fi signal.
    * **2.4 GHz examples:**
        * **Microwave ovens:** Emit very strong, wideband interference in the 2.4 GHz band when in use.
        * **Cordless phones:** Older analog 2.4 GHz phones.
        * **Bluetooth devices:** While often co-existing reasonably well, many Bluetooth devices can cause interference.
        * **Wireless video cameras/security systems:** Especially analog or proprietary digital systems.
        * **Wireless game controllers, baby monitors.**
        * **Faulty electrical devices:** Poorly shielded motors, fluorescent lights, bad power supplies.
    * **5 GHz examples:**
        * **Radar systems:** (weather radar, military radar, airport radar). This is why DFS (Dynamic Frequency Selection) is mandatory in many 5 GHz channels.
        * **Cordless phones:** Some newer cordless phones use 5 GHz.
        * **Wireless backhaul links:** Proprietary point-to-point wireless links.

---

### Impact of Interference

* **
