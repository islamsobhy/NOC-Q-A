
### Scenario 1: Intermittent Connectivity on the 2.4 GHz Band in a Dense Office

* **Problem Description:** Users in a specific open-plan office area are complaining about frequent disconnects and extremely slow speeds, but only when connected to the "Office-Wi-Fi" SSID on the 2.4 GHz band. 5 GHz performance seems acceptable.
* **Symptoms:** High latency, frequent retransmissions, clients dropping off the network, very low throughput.
* **Likely Culprits/Troubleshooting Steps:**
    1.  **Interference (Non-Wi-Fi):** The 2.4 GHz band is prone to non-802.11 interference.
        * **Action:** Use a **spectrum analyzer** (e.g., Ekahau Sidekick, MetaGeek Chanalyzer) to sweep the 2.4 GHz band in the affected area. Look for signatures of microwave ovens, cordless phones, Bluetooth speakers, or even faulty electrical equipment operating in the band.
    2.  **Interference (Wi-Fi - ACI/CCI):** Too many APs on overlapping or same channels.
        * **Action:** Verify **channel planning** in the affected area. Are non-overlapping channels (1, 6, 11) strictly adhered to? Are neighboring APs (internal or external) on the same or adjacent channels with high power? Check **channel utilization** metrics on the WLC/cloud platform.
    3.  **Channel Width:** 40 MHz channels in 2.4 GHz exacerbate interference.
        * **Action:** Ensure all APs in the 2.4 GHz band are configured for **20 MHz channel width**.
    4.  **Client Band Steering:** Many clients might be stuck on 2.4 GHz.
        * **Action:** Verify **Band Steering** is enabled and effective to encourage clients to move to the less congested 5 GHz band.

---

### Scenario 2: New Corporate Laptops Failing to Authenticate to "Corp-Secure" SSID

* **Problem Description:** A batch of new corporate laptops, freshly issued, cannot connect to the "Corp-Secure" SSID, which uses WPA2-Enterprise with 802.1X authentication. Older laptops connect fine. The users report "Authentication failed."
* **Symptoms:** Client repeatedly tries to connect, fails, no IP address obtained. RADIUS server logs might show "Access-Reject."
* **Likely Culprits/Troubleshooting Steps:**
    1.  **802.1X Supplicant Configuration:** New laptops might have different default supplicant settings.
        * **Action:** Verify the 802.1X supplicant configuration on a failing laptop. Is it configured for the correct EAP type (e.g., PEAP-MSCHAPv2, EAP-TLS)? Are the correct server certificates trusted? Is the correct username/password being sent? (A typical issue is not trusting the RADIUS server's certificate).
    2.  **RADIUS Server Logs:** The RADIUS server is the source of truth for authentication failures.
        * **Action:** Check the **RADIUS server (AAA server)** logs (e.g., Cisco ISE, Aruba ClearPass, Microsoft NPS). Look for the specific "Access-Reject" reason. Common reasons include "Invalid credentials," "User not found," "Certificate validation failed," or "Policy denied."
    3.  **WLC/AP Logs:**
        * **Action:** Check logs on the Wireless LAN Controller (WLC) or the specific Access Point. This can show authentication failures and potentially hints about the EAP exchange.
    4.  **Client Machine/Domain Join:** New machines might not be domain-joined correctly, affecting machine-based authentication.
        * **Action:** Confirm the laptops are correctly joined to the domain and have acquired necessary certificates or credentials for machine authentication if applicable.

---

### Scenario 3: VoIP Phones Dropping Calls When Users Walk Between Floors

* **Problem Description:** Users with Wi-Fi VoIP phones experience dropped calls or significant audio degradation when moving between floors in a multi-story building. Within a single floor, calls are stable.
* **Symptoms:** Call drops, one-way audio, choppy audio, long delays during mobility.
* **Likely Culprits/Troubleshooting Steps:**
    1.  **L3 Roaming Configuration:** Moving between floors often means crossing Layer 3 boundaries (different VLANs/subnets).
        * **Action:** Verify **L3 Roaming (Mobility Anchor/Mobility Group)** configuration on the WLCs. Ensure seamless IP address preservation is working across subnets. Check if client tunnel aggregation is correctly configured.
    2.  **802.11r (Fast BSS Transition):** Critical for quick re-authentication.
        * **Action:** Ensure **802.11r (Fast BSS Transition)** is enabled on the SSID used by VoIP phones. This significantly reduces authentication delay during roaming.
    3.  **RF Coverage Gaps:** Gaps in coverage at stairwells or elevators.
        * **Action:** Conduct a **wireless site survey** focusing on transitions points between floors. Look for areas with low RSSI or SNR that could cause sticky clients or slow roaming.
    4.  **Client Roaming Thresholds:** Some VoIP phones have configurable roaming thresholds.
        * **Action:** Check the VoIP phone's client-side Wi-Fi settings. If configurable, ensure its roaming threshold is set appropriately (e.g., not waiting too long before roaming).

---

### Scenario 4: Guest Wi-Fi Clients Stuck on Captive Portal

* **Problem Description:** Guests connect to the "Guest-Wi-Fi" SSID but are unable to get past the captive portal login page. They cannot access the internet after connecting.
* **Symptoms:** Browser redirection to captive portal fails, or login credentials are accepted but no internet access is granted.
* **Likely Culprits/Troubleshooting Steps:**
    1.  **DNS Resolution for Portal:** Clients need to resolve the captive portal's URL.
        * **Action:** Verify that initial DNS resolution is allowed for the captive portal's FQDN (and any external authentication server it relies on) *before* authentication. Ensure DHCP is providing correct DNS server IPs.
    2.  **Pre-Authentication ACLs/Firewall Rules:** Traffic to the portal and any authentication servers must be permitted.
        * **Action:** Check pre-authentication ACLs on the WLC/firewall to ensure that only DNS and HTTP/HTTPS traffic to the captive portal and its authentication sources (e.g., RADIUS server, social login provider) are permitted for unauthenticated clients.
    3.  **DHCP Issues:** Clients might not be getting a valid IP address from DHCP.
        * **Action:** Verify that the **DHCP scope** for the guest VLAN has available addresses and that the **DHCP server** (or relay agent) is correctly configured and reachable from the guest VLAN. Check client IP address on their device.
    4.  **Captive Portal Server Status:** The portal server itself might be down or unresponsive.
        * **Action:** Check the status of the captive portal server (if external) or the WLC's internal portal service. Check logs on the portal server for authentication failures.

---

### Scenario 5: Slow Wi-Fi Performance on Specific Devices (Wi-Fi 5 vs. Wi-Fi 6)

* **Problem Description:** A user with a new Wi-Fi 6 laptop complains about slow speeds, while a colleague with an older Wi-Fi 5 laptop seems to perform better on the same SSID. All APs are Wi-Fi 6 capable.
* **Symptoms:** Lower-than-expected throughput on newer devices, potentially higher retransmission rates on the Wi-Fi 6 client.
* **Likely Culprits/Troubleshooting Steps:**
    1.  **Driver Issues:** New Wi-Fi 6 NICs might have unstable drivers.
        * **Action:** Check and update the wireless adapter drivers on the new Wi-Fi 6 laptop.
    2.  **802.11ax Configuration:** Wi-Fi 6 features might not be fully enabled or optimized.
        * **Action:** Verify that **802.11ax (Wi-Fi 6)** features (like OFDMA, MU-MIMO) are enabled and configured correctly on the APs and WLC. Check channel width configuration (e.g., are you using 80 MHz or 160 MHz in 5 GHz if supported and desired?).
    3.  **Interference/Congestion (even on 5 GHz):**
        * **Action:** Even 5 GHz can suffer from interference, especially in very dense environments. Use a spectrum analyzer to rule out external sources. Check channel utilization to see if the channel is generally busy.
    4.  **BSS Coloring Issues:** If enabled, potential misconfiguration or unique interference patterns.
        * **Action:** Review **BSS Coloring** implementation. While it helps reduce interference, misconfiguration or unusual RF conditions could impact it.
    5.  **TWT Interaction:** Target Wake Time might be causing unexpected delays for a specific client.
        * **Action:** If enabled, review **TWT** settings, although less likely to cause a general slow down, it could affect specific device types.

---

### Scenario 6: Unexpected IP Address Conflicts After Wireless Network Expansion

* **Problem Description:** After adding new APs and expanding Wi-Fi coverage to a new wing, users in that wing are reporting intermittent connectivity and "IP Address Conflict" warnings on their devices.
* **Symptoms:** Devices constantly dropping and rejoining, IP conflict pop-ups, difficulty obtaining an IP address, high `DHCP NAK` or `DHCP Decline` messages in server logs.
* **Likely Culprits/Troubleshooting Steps:**
    1.  **DHCP Scope Exhaustion or Misconfiguration:** The new wing might have more clients than anticipated, or the DHCP scope is too small.
        * **Action:** Check the **DHCP server (AAA if integrated)** to see the current lease utilization for the affected VLAN/subnet. Ensure the DHCP scope size is adequate. Look for multiple `DHCP Decline` messages for the same IP.
    2.  **Duplicate Static IP:** A device with a static IP address within the DHCP pool.
        * **Action:** Identify if any static IP addresses are configured within the dynamic DHCP range. Use ARP tables (`show ip arp`) on routers/switches to find conflicting MAC addresses.
    3.  **Rogue DHCP Server:** An unauthorized device (e.g., a home router plugged in) is handing out IP addresses.
        * **Action:** Use a Wi-Fi analyzer tool or WLC rogue detection features to scan for unauthorized DHCP servers. Check wired switch ports for suspicious devices.
    4.  **DHCP Relay Agent Issues:** If the DHCP server is off-subnet, the relay might be misconfigured.
        * **Action:** Verify the **DHCP Relay Agent** (IP Helper-Address) configuration on the Layer 3 switch/router for the affected VLAN. Ensure it points to the correct DHCP server IP.

---

### Scenario 7: All Clients Suddenly Lose Connectivity on a Specific SSID

* **Problem Description:** All clients connected to the "Marketing-SSID" suddenly lose Wi-Fi connectivity across the entire building. Other SSIDs are functioning normally.
* **Symptoms:** Clients unable to associate or obtain an IP, "No Internet Access" messages, SSID still visible.
* **Likely Culprits/Troubleshooting Steps:**
    1.  **VLAN Mismatch/Problem:** The most common cause for a single SSID failure.
        * **Action:** Verify the **VLAN ID** configured for "Marketing-SSID" on the WLC/cloud platform. Check the **trunk port configuration** on the switch connected to the APs to ensure that VLAN is allowed and correctly configured (e.g., native VLAN mismatch, missing VLAN on the trunk).
    2.  **DHCP Scope Issues for that VLAN:** If clients are associating but not getting IPs.
        * **Action:** Check the **DHCP server** serving the Marketing VLAN. Is the scope active? Is it exhausted? Is the DHCP relay agent for that VLAN configured correctly and routing to the DHCP server?
    3.  **Authentication Server (RADIUS/AAA) Problem for that SSID:** If WPA2-Enterprise is used.
        * **Action:** Check the **RADIUS server (AAA)** status and logs. Is it reachable from the WLC/APs? Are there any errors related to the Marketing user group or specific policies applied to that SSID?
    4.  **SSID Configuration Change:** A recent configuration push might have broken something.
        * **Action:** Review recent configuration changes or firmware upgrades on the WLC/cloud platform specifically affecting the "Marketing-SSID."

---

### Scenario 8: Legacy Industrial Devices Cannot Connect to New Wi-Fi 6 Network

* **Problem Description:** Several older industrial devices (e.g., barcode scanners, legacy PLCs) that only support 802.11g (2.4 GHz) cannot connect to the new Wi-Fi 6 network's "Factory-Net" SSID.
* **Symptoms:** Devices fail to associate or constantly drop connection, even when close to an AP.
* **Likely Culprits/Troubleshooting Steps:**
    1.  **Legacy Rate Support:** New Wi-Fi 6 APs might have disabled older, slower data rates.
        * **Action:** Check the radio settings on the APs for the 2.4 GHz band. Ensure **802.11b/g legacy data rates** are enabled (e.g., 1, 2, 5.5, 11 Mbps basic rates). Sometimes these are disabled by default for performance optimization.
    2.  **802.11ax Only Mode:** APs might be configured to only allow Wi-Fi 6 clients.
        * **Action:** Verify the 2.4 GHz radio is not set to an "802.11ax only" mode. It should be "mixed mode" (802.11b/g/n/ax).
    3.  **Antenna Compatibility:** Very old devices might have specific antenna requirements.
        * **Action:** While less common, ensure antenna compatibility if external antennas are used.
    4.  **Encryption Compatibility:** Very old devices might only support WEP or WPA.
        * **Action:** Confirm the security settings on the SSID match the capabilities of the legacy devices (e.g., some old devices struggle with WPA2-AES). If security must be maintained, consider a dedicated SSID for legacy devices with less stringent but still acceptable security, or a separate VLAN.

---

### Scenario 9: High Number of Unidentified MAC Addresses on Guest VLAN

* **Problem Description:** The network monitoring system is reporting an unusually high number of unique MAC addresses associated with the guest VLAN, many of which are only connected for very short periods.
* **Symptoms:** Rapid turnover of MAC addresses, potentially high DHCP utilization, possible performance degradation due to churn.
* **Likely Culprits/Troubleshooting Steps:**
    1.  **MAC Randomization (Client Feature):** Modern client devices increasingly use MAC address randomization for privacy.
        * **Action:** Recognize that this is **normal behavior** for many smartphones (iOS 14+, Android 10+). This isn't a problem but a privacy feature. While it makes tracking individual devices harder, it typically doesn't cause performance issues by itself unless combined with aggressive DHCP lease times.
    2.  **Short DHCP Lease Times on Guest Network:** Very short lease times cause frequent DHCP renewals and new requests.
        * **Action:** Review the **DHCP lease time** for the guest VLAN. If it's very short (e.g., 30 minutes), consider extending it to a few hours or a day, unless there's a specific reason for extreme brevity.
    3.  **Guest Portal Session Management:** The captive portal session might be shorter than the DHCP lease.
        * **Action:** Ensure the session timeout on the **captive portal** is aligned with DHCP lease times or client expectations to prevent rapid re-authentication cycles.
    4.  **Wi-Fi Scanners/Attack Tools:** Could be legitimate scanners or malicious tools.
        * **Action:** Use **WIPS/Rogue AP detection** features to identify any suspicious scanning activity.

---

### Scenario 10: Specific Application (e.g., Video Conferencing) Experiencing Jitter/Lag Over Wi-Fi

* **Problem Description:** Users report significant jitter and lag during video conference calls (e.g., Zoom, Teams) when connected to Wi-Fi, but wired connections are fine. Other applications seem less affected.
* **Symptoms:** Pixelation, audio drops, freezing frames, desynchronization in video calls.
* **Likely Culprits/Troubleshooting Steps:**
    1.  **QoS (Quality of Service) Misconfiguration/Missing:** Video traffic is highly latency-sensitive.
        * **Action:** Verify that **WMM (Wi-Fi Multimedia / 802.11e)** is enabled on the SSID. Ensure **DSCP marking** from the wired network is correctly mapped to **WMM Access Categories** on the AP/WLC for video and voice traffic.
    2.  **Airtime Contention/Interference:** Video requires sustained bandwidth and low retransmissions.
        * **Action:** Check **channel utilization** on the APs. Even if speeds seem okay for other apps, high utilization or hidden node problems can impact real-time traffic. Use a spectrum analyzer to find non-Wi-Fi interference.
    3.  **Low SNR/Poor Signal:** Even short drops in signal quality can severely impact video.
        * **Action:** Check the **SNR and RSSI** of affected clients. Consider AP placement adjustments or power tuning to improve signal quality for video conference areas.
    4.  **Wireless Standard Features:**
        * **Action:** If using Wi-Fi 6 (802.11ax), ensure features like **OFDMA** and **MU-MIMO** are optimized to provide better efficiency for mixed traffic, which includes video.
    5.  **Bandwidth Saturation (Uplink/Downlink):**
        * **Action:** Monitor uplink and downlink bandwidth usage on the APs. Even if the total capacity is high, if the available bandwidth to specific clients drops below the video stream's requirement, issues will occur. Ensure adequate bandwidth exists end-to-end.

---
