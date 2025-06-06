# CAPWAP 

## **Control And Provisioning of Wireless Access Points (CAPWAP)**

CAPWAP is a protocol that allows Wireless LAN Controllers (WLC) to manage multiple Access Points (APs) remotely. It operates over two main UDP ports:
- **5246** for control messages (encrypted with DTLS)
- **5247** for data messages (can be encrypted or unencrypted)

## **CAPWAP Control Messages**
Control messages are essential for communication between APs and WLC. These messages travel through an **encrypted DTLS tunnel** on UDP **5246**.

### **Types of Control Messages:**
1. **Discovery Request / Response**
   - The AP sends a request to discover available WLCs.
   - The WLC responds with availability information.

2. **Join Request / Response**
   - The AP attempts to join a WLC.
   - The WLC authenticates and accepts the AP.

3. **Configuration Request / Response**
   - The AP requests its configuration from the WLC.
   - The WLC sends SSID, VLAN, encryption settings, and radio parameters.

4. **Image Download Request / Response**
   - If the AP does not have the correct firmware, the WLC provides an update.

5. **Keepalive / Echo Request / Response**
   - AP and WLC exchange messages to verify connectivity.
   - If the AP loses contact with WLC, it may reboot or connect to another WLC.

6. **Statistics and Event Messages**
   - AP sends statistics and logs to the WLC (radio status, connected clients, interference levels).

7. **De-authentication and Disconnection**
   - WLC can disconnect an AP due to policy violations or malfunctions.

## **CAPWAP Connection Process (AP to WLC)**
1. **Boot**
   - The AP obtains an IP address via DHCP (optional: DNS lookup for WLC).

2. **Discovery**
   - The AP searches for a WLC using:
     - **DHCP Option 43**
     - **DNS (CISCO-CAPWAP-CONTROLLER.localdomain)**
     - **Broadcast or Unicast to known WLC IPs**

3. **Join**
   - The AP authenticates itself to the WLC using certificates or PSK.

4. **Image Download (if needed)**
   - If the AP does not have the correct firmware, the WLC provides it.

5. **Configuration**
   - The WLC sends the AP its full configuration (SSID, VLANs, power settings, security settings).

6. **Run Mode (Operational Mode)**
   - The AP begins providing Wi-Fi connectivity to clients.

7. **Heartbeat / Keepalive**
   - The AP periodically communicates with the WLC to confirm it is still online.

## **CAPWAP Data Tunnel (Data Plane)**
The data tunnel transmits client data between the AP and the WLC.
- Operates over **UDP 5247**.
- Can be **encrypted or unencrypted**.
- Supports **local switching** (direct routing at AP) or **centralized switching** (tunnel traffic through WLC).

## **CAPWAP Architectures: Centralized vs. FlexConnect**
1. **Centralized (Local Mode)**
   - All traffic is tunneled to the WLC.
   - Ideal for large networks requiring centralized security and management.

2. **FlexConnect (formerly H-REAP)**
   - APs can function without constant WLC connectivity.
   - Data can be **locally switched** at the AP instead of being tunneled to WLC.
   - Useful for branch offices or small networks with minimal WLC dependence.

## **Wireshark CAPWAP Debugging**
To analyze CAPWAP communication, use the following Wireshark filter:
```bash
udp.port == 5246 || udp.port == 5247
```
Key CAPWAP packets:
- **Discovery Request / Response**
- **Join Request / Response**
- **Keepalive (Echo Request / Response)**
- **Data Tunnel Traffic**

# Difference Between CAPWAP and LWAPP

Before CAPWAP, Cisco used **LWAPP (Lightweight Access Point Protocol)** to manage wireless access points. CAPWAP replaced LWAPP because it supports encryption and newer wireless standards (802.11n and beyond).

| **Feature**                | **CAPWAP**           | **LWAPP**               |
|----------------------------|----------------------|-------------------------|
| Control Message Encryption | Yes (DTLS)           | Yes                     |
| Data Packet Encryption     | Optional             | No                      |
| Standardized by IETF       | Yes                  | No (Cisco proprietary)  |
| Supports 802.11n and newer | Yes                  | No                      |
| Transport Protocol         | UDP                  | UDP                     |
| Ports Used                 | 5246 (Control), 5247 (Data) | 12222 (Control), 12223 (Data) |

In summary, CAPWAP is a more secure and flexible replacement for LWAPP, allowing support for modern wireless technologies while maintaining central management of access points.

# Datagram Transport Layer Security (DTLS) and Split MAC

## **Datagram Transport Layer Security (DTLS)**

**DTLS (Datagram Transport Layer Security)** is a protocol designed to provide security for datagram-based applications. It is based on TLS (Transport Layer Security) but is specifically optimized for UDP (User Datagram Protocol), which does not guarantee reliable transmission. DTLS ensures data integrity, confidentiality, and authentication for applications that require low-latency communication, such as voice, video, and real-time data streaming.

In the context of **CAPWAP**, DTLS is used to encrypt control messages exchanged between the Access Point (AP) and the Wireless LAN Controller (WLC), providing a secure channel for managing wireless devices. By using DTLS, CAPWAP ensures that the communication remains secure, even in an environment where UDP is used, making it suitable for large-scale networks.

## **Split MAC**

**Split MAC** is a design in wireless networks that divides the MAC (Media Access Control) functionality between the Access Point (AP) and the Wireless LAN Controller (WLC). This allows the controller to manage the control plane (e.g., authentication, association, and encryption) while the AP handles the data plane (e.g., sending and receiving actual data packets).

In **Split MAC architecture**, the AP can operate independently and handle tasks like radio management, while the WLC handles the high-level control tasks such as client management and configuration. This design improves scalability and reduces the load on individual APs. It also simplifies network management by centralizing control in the WLC, while the APs remain responsible for data transmission.

## **Summary**

- **DTLS** provides encryption for control messages in CAPWAP, ensuring secure communication between APs and WLCs.
- **Split MAC** separates control and data functions between the AP and WLC, enhancing scalability and efficiency in wireless network management.


## **Summary**
- **CAPWAP enables remote AP management through WLC using UDP 5246 (control) and UDP 5247 (data).**
- **APs follow a process: Boot → Discovery → Join → Configuration → Run Mode.**
- **FlexConnect allows APs to operate independently if the WLC is unavailable.**
- **Wireshark debugging can help analyze CAPWAP communication issues.**

---

