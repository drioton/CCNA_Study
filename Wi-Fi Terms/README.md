# Wireless Network

# Wi-Fi Networking Concepts (CCNA)

## Workgroup Bridge (WGB)
A **Workgroup Bridge** is used to connect wired devices to a Wi-Fi network.

- Acts as a **wireless client** but provides connectivity to multiple wired devices.
- Useful when you have **legacy wired devices** that don’t support Wi-Fi.
- Typically used in **industrial environments** or warehouses.
- **Example:** A switch with multiple wired devices connects to a WGB, which then connects to the Wi-Fi network.

---

## Repeater
A **Repeater** extends Wi-Fi coverage by retransmitting signals.

- **No wired connection** to the network – it only relays signals wirelessly.
- **Downside:** Reduces bandwidth (since it uses the same channel to receive and transmit).
- Used in areas with poor coverage but **not recommended** for high-performance networks.

---

## IBSS (Independent Basic Service Set)
- **Ad-hoc network** – devices communicate **directly** without an AP.
- **Example:** Two laptops connected via Wi-Fi **without a router or AP**.
- Used for **temporary** or **peer-to-peer** connections.

---

## ESS (Extended Service Set)
- A network of **multiple APs connected to the same SSID**.
- Ensures **seamless roaming** for users.
- **Example:** Staying connected to Wi-Fi while moving through a large office or public space.

---

## Beacon
- A small **Wi-Fi management frame** broadcasted by APs.
- Contains **SSID, capabilities, supported data rates**, etc.
- Sent **every 100 ms** by default to help clients discover networks.

---

## BSSID (Basic Service Set Identifier)
- The **MAC address** of the AP’s wireless interface.
- Each **AP + SSID combination** has a unique BSSID.
- Helps differentiate APs in the same ESS.

---

### 💡 Notes
- **WGB** is ideal for industrial or legacy wired setups.
- **Repeaters** are simple but reduce performance.
- **IBSS** is rarely used outside of quick ad-hoc connections.
- **ESS** enables seamless roaming in enterprise networks.
- **Beacons** are essential for network discovery.
- **BSSID** uniquely identifies APs.

---

## Outdoor Bridge
An **Outdoor Bridge** connects two separate wired networks over a wireless link.

- Typically used for **long-distance** connections (e.g., between buildings).
- Can be **Point-to-Point (PtP)** or **Point-to-Multipoint (PtMP)**.
- Requires **line of sight (LoS)** for best performance.
- **Example:** Two offices in different buildings connect using an outdoor Wi-Fi bridge.

---

## Probe Request & Probe Response
### **Probe Request**
- A **Wi-Fi client broadcasts** a Probe Request to discover available networks.
- Sent when a device is **searching for known SSIDs** or **scanning for networks**.
- Can be **directed (specific SSID)** or **broadcasted (any network available)**.

### **Probe Response**
- An **AP replies** with a Probe Response if it matches the requested SSID.
- Contains **SSID, capabilities, and supported data rates**.
- Helps clients decide which AP to connect to.

---

## Passive Scanning
- The Wi-Fi client **listens for Beacons** sent by APs instead of sending Probe Requests.
- Uses **less power** compared to active scanning.
- Common in **mobile devices** to optimize battery life.

---

## Association Request
- A **Wi-Fi client requests to join an AP** after selecting a network.
- Sent **after scanning and authentication**.
- Contains **client MAC address, supported rates, and security details**.

---

## Reassociation Request
- Sent when a **client moves to a different AP within the same ESS**.
- Helps maintain **seamless roaming** in enterprise Wi-Fi networks.
- **Example:** A phone switching APs while moving through an office without losing connection.

---

## Non-Overlapping Channels
- In **2.4 GHz Wi-Fi**, the only **non-overlapping channels** are **1, 6, and 11**.
- In **5 GHz Wi-Fi**, multiple non-overlapping channels exist, depending on regional regulations.
- Using **overlapping channels causes interference** and reduces performance.

---

## Mesh Networking
A **Mesh Network** allows APs to communicate **wirelessly** instead of using wired backhaul.

- APs form a **self-healing network**, automatically routing traffic.
- Useful for **large areas** where cabling is difficult (e.g., outdoor campuses, warehouses).
- Relies on **Mesh Gateways** to connect to the wired network.

---

### 💡 Notes
- **Outdoor Bridges** are used for **long-range** wireless connections.
- **Probe Requests/Responses** help clients discover and connect to Wi-Fi.
- **Passive Scanning** saves battery but may take longer to find networks.
- **Association Requests** establish client connections with an AP.
- **Reassociation Requests** ensure seamless roaming.
- **Non-overlapping channels** reduce interference.
- **Mesh Networks** are ideal for large-scale deployments without extensive cabling.



## MIC (Message Integrity Check)
- Ensures **data integrity** in Wi-Fi communication.
- Prevents **packet tampering** and **man-in-the-middle (MITM) attacks**.
- Used in **WPA, WPA2, and WPA3** security protocols.

---

## EAP (Extensible Authentication Protocol)
A **framework** used for authentication in Wi-Fi security.

- Supports various authentication methods (EAP-TLS, PEAP, EAP-FAST, etc.).
- Used in **enterprise Wi-Fi networks** (802.1X authentication).
- Works with **RADIUS servers** for centralized authentication.

---

## LEAP (Lightweight EAP)
- A **Cisco-proprietary** authentication method.
- Uses **username/password** but has **security weaknesses**.
- **Replaced** by more secure EAP methods (e.g., EAP-FAST).

---

## EAP-FAST (EAP-Flexible Authentication via Secure Tunneling)
- Developed by Cisco to **replace LEAP**.
- Uses a **Protected Access Credential (PAC)** for secure authentication.
- Does **not require certificates**, making it easier to deploy.

---

## PEAP (Protected EAP)
- Creates a **secure TLS tunnel** before transmitting credentials.
- **More secure** than LEAP but requires a **server-side certificate**.
- Commonly used in **enterprise Wi-Fi (802.1X)** with **RADIUS**.

---

## WEP (Wired Equivalent Privacy)
🚨 **Insecure – DO NOT USE** 🚨

- An **old Wi-Fi encryption protocol** that is easily hacked.
- Uses **static keys**, making it vulnerable to attacks.
- **Replaced by WPA, WPA2, and WPA3**.

---

## Open Authentication
- **No authentication required** – any device can connect.
- Used in **public Wi-Fi hotspots** or guest networks.
- Can be combined with **captive portals** for access control.

---

## TKIP (Temporal Key Integrity Protocol)
- Introduced in **WPA** as a **replacement for WEP**.
- Uses **dynamic encryption keys** that change periodically.
- Still **vulnerable** to attacks and was replaced by **CCMP (AES)** in WPA2.

---

## CCMP (Counter Mode CBC-MAC Protocol)
- Based on **AES encryption** – much stronger than TKIP.
- Used in **WPA2 and WPA3** for **strong security**.
- Provides both **encryption** and **data integrity**.

---

## WPA (Wi-Fi Protected Access)
- Introduced as a **temporary fix** for WEP.
- Uses **TKIP** (less secure than AES).
- **No longer considered secure**.

---

## WPA2
- Uses **CCMP (AES encryption)** for stronger security.
- The **current standard** for most Wi-Fi networks.
- Requires **802.1X for enterprise authentication**.

---

## WPA3
- The **latest Wi-Fi security standard**.
- Uses **SAE (Simultaneous Authentication of Equals)** instead of pre-shared keys.
- Provides **better encryption** and protection against **brute-force attacks**.
- **Mandatory for new Wi-Fi devices** since 2020.

---

## PKI (Public Key Infrastructure)
- A **cryptographic system** that uses **certificates** for authentication.
- Used in **EAP-TLS and PEAP** to verify servers and clients.
- Helps prevent **MITM attacks** in enterprise Wi-Fi.

---

### 💡 Notes
- **MIC** protects against packet tampering.
- **EAP** is a framework for secure authentication.
- **LEAP is outdated** – use **EAP-FAST or PEAP** instead.
- **WEP and WPA (TKIP) are insecure** – always use **WPA2 (AES) or WPA3**.
- **PKI** is crucial for certificate-based authentication in enterprise Wi-Fi.

## EWC (Embedded Wireless Controller)
An **EWC (Embedded Wireless Controller)** is a controller that is integrated directly into Cisco **Access Points (APs)**.

- Allows for **centralized management** of Wi-Fi networks without requiring an external wireless controller.
- Ideal for **small to medium-sized networks** with fewer APs.
- Provides features like **security policies**, **RF management**, and **client management**.
- Can be deployed in environments like **small offices**, **retail locations**, and **branch offices**.

# Cisco AP Modes (Detailed Explanation)

Cisco Access Points (APs) can be deployed in various **modes**, each tailored for different network architectures and requirements. Below is a detailed breakdown of each AP mode:

## 1. **Local Mode**
Local mode is the default mode for most Cisco APs.

### Key Features:
- The AP functions as both the **access point** and the **wireless controller**.
- **Client traffic** is sent to the **Wireless LAN Controller (WLC)** for processing and management.
- The AP is **managed centrally** through the WLC, which configures the wireless settings such as SSIDs, security policies, and RF management.
- **High availability** is achieved because the AP relies on the WLC for configuration and data forwarding.
  
### Use Case:
- Ideal for environments where **centralized management** and **control** are required, such as **enterprise networks** or **larger campuses**.

### Advantages:
- Centralized control from WLC.
- Easy to configure and manage multiple APs from a central location.
  
---

## 2. **FlexConnect Mode**
FlexConnect mode is designed for **remote locations** that need **local wireless services** but may not always have a reliable connection to the central WLC.

### Key Features:
- **Local forwarding** of data: When clients connect to the AP, their traffic can be forwarded locally instead of going back to the WLC, reducing WAN traffic.
- The AP is **autonomous** and can continue providing services even if it loses connectivity to the WLC.
- FlexConnect APs can still receive **configuration updates** and **monitoring data** from the WLC when the connection is available.
- Supports **local authentication**, allowing users to authenticate locally in case the WLC is unreachable.
- **Works in conjunction with a WLC** but offers **flexibility** in remote sites.

### Use Case:
- Ideal for **branch offices**, **remote sites**, or **locations with unreliable WAN connectivity**. Also useful when there is **limited bandwidth** between the remote site and the central controller.

### Advantages:
- Allows APs to function independently during network outages.
- Reduces the amount of **WAN traffic** by enabling **local forwarding**.
- Simplifies management by allowing the WLC to configure remote APs centrally.

---

## 3. **Mesh Mode**
Mesh mode is used to extend the **wireless coverage** of a network without needing physical cabling between access points.

### Key Features:
- APs in mesh mode form a **wireless backhaul** network where each AP connects wirelessly to another.
- Supports **Point-to-Point (PtP)** or **Point-to-Multipoint (PtMP)** configurations.
- Ideal for environments where it is **impractical** to run Ethernet cables, such as **outdoor campuses**, **large public spaces**, or **difficult-to-wire areas**.
- Mesh APs can also provide **redundancy** in coverage, so if one AP fails, others in the mesh network can still provide service.

### Use Case:
- Used in large areas where cabling would be too expensive or impractical, such as **outdoor campuses**, **stadiums**, or **warehouses**.
- Also beneficial in scenarios where **mobility** is needed and a **wired infrastructure** is unavailable or infeasible.

### Advantages:
- Extends Wi-Fi coverage to areas where cabling is not possible.
- Provides **redundant paths** for data, improving reliability.
  
---

## 4. **Sniffer Mode**
Sniffer mode turns an AP into a **packet capture device** for troubleshooting and diagnostic purposes.

### Key Features:
- The AP captures **all wireless traffic** on the radio frequency (RF) it is set to monitor.
- It does not forward traffic to clients but instead captures it for analysis by network engineers.
- Captured packets can be analyzed to identify issues such as **interference**, **client connectivity problems**, or **network performance issues**.
- Often used in conjunction with **Wireshark** or other packet analysis tools for a deeper understanding of network traffic.

### Use Case:
- Ideal for **network troubleshooting** and **performance analysis**.
- Used in situations where you need to **monitor traffic** or detect **Wi-Fi issues** such as **RF interference** or **poor client connections**.

### Advantages:
- Helps troubleshoot and analyze wireless network performance.
- Captures real-time traffic for detailed analysis.

---

## 5. **Reboot Mode**
This is not typically listed as a primary operating mode, but it’s a special mode used for restarting the AP.

### Key Features:
- When a Cisco AP is powered down and rebooted, it enters a **reboot mode**.
- This is commonly used for **maintenance** or **configuration changes**.

---

### 💡 Notes:
- **Local Mode** is ideal for centralized control and high availability.
- **FlexConnect** allows APs to work independently when there’s no connection to the WLC.
- **Mesh Mode** extends wireless coverage without needing cables, ideal for large outdoor areas.
- **Sniffer Mode** is useful for network diagnostics and troubleshooting.
  
---




































