# Network Design Layers & Related Technologies

### Hierarchical Network Design Overview

| Layer              | Purpose                                                         | Devices                   | Key Technologies                           |
| ------------------ | --------------------------------------------------------------- | ------------------------- | ------------------------------------------ |
| Access Layer       | Connects end devices to the network                             | Switches (PoE, edge), APs | DHCP Snooping, Port Security, PoE          |
| Distribution Layer | Aggregates access switches, enforces policy, inter-VLAN routing | Layer 3 Switches          | Routing, ACLs, Redundancy (HSRP/VRRP), QoS |
| Core Layer         | High-speed backbone, connects distribution layers               | Core switches or routers  | High throughput, redundancy, low latency   |

### Layer Identification: How to Recognize Them

| Clue               | Access Layer         | Distribution Layer       | Core Layer            |
| ------------------ | -------------------- | ------------------------ | --------------------- |
| Devices connected? | End-users (PCs, APs) | Access switches          | Distribution switches |
| Switch type?       | Layer 2 w/ some L3   | Layer 3 switch           | High-end L3 switch    |
| Functions?         | VLAN, Port security  | Inter-VLAN routing, ACLs | Fast switching        |
| Physical topology? | Closest to users     | Between access and core  | Central/backbone      |

### Two-Tier vs Three-Tier Architecture

| Model   | Description                                              | When Used                  |
| ------- | -------------------------------------------------------- | -------------------------- |
| 3-Tier  | Access ↔ Distribution ↔ Core                             | Large enterprise networks  |
| 2-Tier  | Access ↔ Distribution (Core collapsed into Distribution) | Small to medium businesses |
| No Core | Core not needed in small networks                        | Very small LAN setups      |

### Access Layer Features (Edge Technologies)

| Feature                   | Description                                                    |
| ------------------------- | -------------------------------------------------------------- |
| PoE (Power over Ethernet) | Supplies power to devices like IP phones, cameras via Ethernet |
| DHCP Snooping             | Prevents rogue DHCP servers; builds binding table              |
| Port Security             | Restricts MAC addresses allowed on a switchport                |
| Storm Control             | Prevents broadcast/multicast floods                            |

### Power & Fiber Terms

| Term     | Description                                                               |
| -------- | ------------------------------------------------------------------------- |
| PSE      | Power Sourcing Equipment (e.g., switch providing PoE)                     |
| PD       | Powered Device (e.g., IP camera, phone, AP)                               |
| Multigig | Supports speeds above 1 Gbps on existing Cat5e/Cat6 (e.g., 2.5G, 5G, 10G) |
| OM Fiber | Optical Multimode (OM1–OM5), used for short distances, larger cores       |

###  Distribution Layer Services

| Function           | Why It Matters                                               |
| ------------------ | ------------------------------------------------------------ |
| Inter-VLAN Routing | Connects different VLANs via Layer 3 interfaces              |
| ACLs               | Filters traffic based on IP/port, enforces security policies |
| QoS                | Prioritizes critical traffic like voice/video                |
| Redundancy         | Uses protocols like HSRP, VRRP for failover                  |

### Core Layer Characteristics

| Feature         | Description                                |
| --------------- | ------------------------------------------ |
| High Speed      | 10G/40G/100G interfaces for fast switching |
| Redundant Paths | Dual connections for uptime                |
| Minimal Policy  | Avoid ACLs or QoS for speed                |

### How to Practice Identifying Layers

<b>Look at device role:</b>

Connecting clients → Access Layer

Aggregating and routing → Distribution Layer

Backbone connections only → Core Layer

<b>Number of devices:</b>

Single building → Two-tier

Multi-building/multi-campus → Three-tier

<b>Simulation Tools:</b>

Use Cisco Packet Tracer or GNS3

Build simple topologies to recognize layers

### Additional Key Concepts

| Concept                              | Description                                                                       |
| ------------------------------------ | --------------------------------------------------------------------------------- |
| Layer 2 vs Layer 3 Switch            | Layer 2: MAC-based forwarding. Layer 3: IP-based routing, SVIs, routing protocols |
| SVIs (Switched Virtual Interfaces)   | Logical interfaces on Layer 3 switches used for inter-VLAN routing                |
| Uplinks & Trunk Ports                | Uplinks: connections to higher layers. Trunks carry multiple VLANs using 802.1Q   |
| EtherChannel                         | Bundles multiple physical links into one logical link (LACP/PAgP)                 |
| Redundancy Protocols                 | HSRP, VRRP, GLBP – prevent single point of failure at Distribution/Core           |
| First Hop Redundancy Protocol (FHRP) | Ensures default gateway availability (HSRP/VRRP for CCNA)                         |
| Spanning Tree Protocol (STP)         | Prevents Layer 2 loops across Access/Distribution layers                          |
| Load Balancing                       | Used at Distribution/Core to optimize traffic paths                               |

### Redundancy & High Availability

| Technique           | Layer  | Description                                                     |
| ------------------- | ------ | --------------------------------------------------------------- |
| Dual-Homed Access   | Access | Each access switch connects to two distribution switches        |
| Redundant Core      | Core   | Two core switches, each connected to both distribution switches |
| Dual Power Supplies | All    | Physical redundancy at device level                             |
| Dual Paths          | All    | Two paths from access → distribution or distribution → core     |

### Security at Access Layer

| Feature                      | Description                                                            |
| ---------------------------- | ---------------------------------------------------------------------- |
| Port Security                | Limits the number of MACs per port; protects against MAC flooding      |
| DHCP Snooping                | Filters DHCP replies; maintains trusted/untrusted ports                |
| Dynamic ARP Inspection (DAI) | Validates ARP packets using DHCP Snooping binding table                |
| IP Source Guard              | Prevents spoofed IP addresses on untrusted ports                       |
| BPDU Guard                   | Disables a port that receives unexpected BPDUs (protects STP topology) |
| Root Guard                   | Prevents switch from becoming STP root on access layer                 |

###  PoE Details

| Category                       | Info                                                                |
| ------------------------------ | ------------------------------------------------------------------- |
| PoE Standards                  | IEEE 802.3af (15.4W), 802.3at (30W, PoE+), 802.3bt (60–100W, PoE++) |
| PSE (Power Sourcing Equipment) | Switch providing power (typically access layer)                     |
| PD (Powered Device)            | Receives power (IP Phone, AP, Camera)                               |

### Multigigabit Ethernet

| Feature          | Description                                                 |
| ---------------- | ----------------------------------------------------------- |
| Use Case         | Modern Wi-Fi APs (Wi-Fi 6+), higher throughput over Cat5e/6 |
| Speeds Supported | 1G, 2.5G, 5G, 10G                                           |
| Appears On       | Access and Distribution Switches                            |
| Benefit          | Avoid replacing cabling for higher speed                    |

### Fiber Types – OM Overview
 
 | Type | Core Size | Bandwidth (MHz·km) | Max Distance (Gbps) | Typical Use                   |
| ---- | --------- | ------------------ | ------------------- | ----------------------------- |
| OM1  | 62.5µm    | 200–500            | 275m @ 1 Gbps       | Legacy                        |
| OM2  | 50µm      | 500                | 550m @ 1 Gbps       | Short-range data center       |
| OM3  | 50µm      | 2000               | 300m @ 10 Gbps      | Laser optimized               |
| OM4  | 50µm      | 4700               | 400m @ 10 Gbps      | Extended OM3                  |
| OM5  | 50µm      | 28000              | Up to 100G (short)  | Newest multimode, WDM support |
