# Spanning Tree Protocol (STP) Overview
 STP, RTSP, Root Bridge, Cost, Terminology   


### What is STP?
Spanning Tree Protocol (STP) is a network protocol designed to prevent loops in Ethernet networks. It ensures a loop-free topology by disabling redundant links while maintaining fault tolerance. STP was originally developed by Radia Perlman and standardized as IEEE 802.1D.

### How Does STP Work?
STP uses the spanning tree algorithm to detect redundant paths and selectively disable them, preventing broadcast storms and network instability. It elects a root bridge and calculates the best paths from all switches to this root.

### History and Versions of STP
- **IEEE 802.1D (Original STP)** – Introduced in 1990, slow convergence time (30-50 seconds).
- **IEEE 802.1w (Rapid Spanning Tree Protocol - RSTP)** – Faster convergence (less than 10 seconds).
- **IEEE 802.1s (Multiple Spanning Tree Protocol - MSTP)** – Supports multiple spanning trees for VLANs.
- **Cisco Proprietary:**
  - **Per-VLAN Spanning Tree (PVST)** – Runs a separate STP instance per VLAN.
  - **Rapid Per-VLAN Spanning Tree (RPVST)** – Cisco’s enhanced version of RSTP per VLAN.

### Advantages of STP
- Prevents broadcast storms.
- Enables redundancy without causing loops.
- Ensures a stable network topology.

### Disadvantages and Risks of STP
- Convergence time can be slow in classic STP.
- Can be vulnerable to attacks like **BPDU spoofing** and **Rogue Switch attacks**.
- Misconfigurations may lead to instability.

### STP Terminology
- **Root Bridge** – The switch with the lowest Bridge ID, which becomes the reference point.
- **Bridge ID** – Composed of priority value and MAC address.
- **Port Cost** – Determines the best path to the root bridge.
- **Root Port** – The port with the lowest cost to the root bridge.
- **Designated Port** – The port that forwards traffic for a specific segment.
- **Blocked Port** – A port disabled to prevent loops.

### Cost Values Based on Speed
- 10 Mbps → Cost = 100
- 100 Mbps → Cost = 19
- 1 Gbps → Cost = 4
- 10 Gbps → Cost = 2

### STP Convergence Time
- **Classic STP (802.1D):** 30-50 seconds.
- **Rapid STP (802.1w):** Less than 10 seconds.

### STP Security and Rogue Switch Attacks
A rogue switch attack involves an unauthorized switch being introduced into the network and sending superior BPDU packets to take over as the root bridge, disrupting network traffic.

#### How to Protect Against STP Attacks
- **BPDU Guard** – Disables ports receiving unexpected BPDUs.
- **Root Guard** – Prevents unauthorized switches from becoming root.
- **Loop Guard** – Prevents alternate paths from becoming forwarding if BPDUs are lost.

### STP Features and Differences
- **PortFast** – Bypasses STP listening/learning states, immediately forwarding traffic.
- **BPDU Filter** – Prevents BPDUs from being sent/received (only used with PortFast).
- **BPDU Guard** – Disables ports receiving unexpected BPDUs.
- **Loop Guard** – Prevents alternate ports from becoming designated ports.

### Difference Between PortFast, BPDU Filter, and BPDU Guard
- **PortFast** is used on end-host ports, skipping STP states for fast activation.
- **BPDU Filter** is only applied with PortFast, blocking BPDU processing.
- **BPDU Guard** shuts down a port if it receives BPDUs, preventing rogue switch attacks.

### RSTP (Rapid Spanning Tree Protocol) Port Roles
RSTP introduces additional port roles that enable faster convergence:

- **Root Port (RP)** – The port with the lowest cost to the root bridge, always forwarding.
- **Designated Port (DP)** – The best port for a given segment, forwarding traffic.
- **Alternate Port (AP)** – A backup port for the root port, blocked but ready to take over if needed.
- **Backup Port (BP)** – A backup port for a designated port on the same segment, blocked.
- **Disabled Port** – A port that is administratively shut down.

### Why RSTP Converges Faster
- Uses **proposal/agreement mechanism** instead of timers.
- Ports can transition to forwarding state immediately if certain conditions are met.
- Edge ports (PortFast) and point-to-point links help accelerate the process.

### Port Roles Table

| **Port Type**      | **Function** |
|--------------------|----------------------------------------------------|
| **Root Port (RP)**   | Best path to the Root Bridge, forwards traffic. |
| **Designated Port (DP)** | Forwards traffic for a specific network segment. |
| **Alternate Port (AP)** | Backup for the Root Port, blocked but ready to take over. |
| **Backup Port (BP)** | Backup for a Designated Port on the same segment, blocked. |
| **Disabled Port** | Administratively shut down, does not participate in STP. |

### STP Configuration on Cisco Switches

#### Enabling STP (Default is PVST+ on Cisco Switches)
```
Switch(config)# spanning-tree mode pvst
```

#### Configuring RSTP
```
Switch(config)# spanning-tree mode rapid-pvst
```

#### Setting the Root Bridge Manually

```
Switch(config)# spanning-tree vlan 10 priority 0
```

These commands are specific to Cisco devices and automatically set the root bridge with a priority of 24576 (primary) and 28672 (secondary).   

### Sets the device as the root bridge with the lowest priority.


These values fall within the standard range when configuring the root bridge, where the root bridge with the lowest priority is selected.

```
spanning-tree vlan <vlan_id> root primary
```

### Sets the device as the root bridge with the higher priority. 

Sets the device as the root bridge with a priority higher than the primary, but still lower than other devices in the network.

```
spanning-tree vlan <vlan_id> root secondary
```
Using these commands is useful when you want to ensure redundancy in case the primary root bridge fails, and the network automatically switches to the secondary root bridge.

#### Enabling PortFast
```
Switch(config-if)# spanning-tree portfast
```

#### Enabling BPDU Guard
```
Switch(config-if)# spanning-tree bpduguard enable
```

### MSTP and VLANs
MSTP (Multiple Spanning Tree Protocol) allows grouping VLANs into different spanning-tree instances, reducing CPU overhead. Example:

In **MSTP (Multiple Spanning Tree Protocol)**, an **instance** refers to a group of VLANs that share the same spanning-tree process. Instead of running a separate STP instance for each VLAN (as in PVST+), MSTP allows multiple VLANs to be mapped to a single spanning-tree instance, reducing the number of STP processes and improving network efficiency.

### **How MSTP Instances and VLANs Work**
- VLANs are assigned to **MST instances (MSTI)**.  
- Each MST instance has its own **root bridge** and spanning-tree topology.  
- A switch can participate in multiple MST instances, with different VLANs assigned to different instances.  
- MSTP operates within a **region**, where all switches must have the same MST configuration (name, revision number, and VLAN-to-instance mapping) to function correctly.

By grouping VLANs into instances, MSTP optimizes convergence and reduces the processing overhead compared to running an individual STP instance per VLAN.


```
Switch(config)# spanning-tree mode mst
Switch(config)# spanning-tree mst configuration
Switch(config-mst)# instance 1 vlan 10,20,30
Switch(config-mst)# instance 2 vlan 40,50,60
Switch(config-mst)# exit
```

### CCNA Exam Practice Questions
1. What is the purpose of Spanning Tree Protocol (STP)?
2. How does STP prevent switching loops?
3. What is the difference between STP, RSTP, and MSTP?
4. How does RSTP achieve faster convergence compared to classic STP?
5. What are the different port roles in RSTP, and how do they function?
6. What is the significance of the Bridge ID in STP elections?
7. How can STP be exploited using a rogue switch attack?
8. What mechanisms can be used to secure a network from BPDU-based attacks?
9. What is the difference between Alternate and Backup ports in RSTP?
10. What is the function of BPDU Guard and Root Guard?

By understanding and properly configuring STP and RSTP, networks can be protected against loops while minimizing vulnerabilities to attacks and misconfigurations.

