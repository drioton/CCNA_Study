# CDP and LLDP

### CDP (Cisco Discovery Protocol) and LLDP (Link Layer Discovery Protocol)
Both CDP and LLDP are protocols used for discovering devices on the network, but they have some important differences, especially in terms of compatibility and scope.

## What is CDP?
Cisco Discovery Protocol (CDP) is a proprietary protocol developed by Cisco. It operates at Layer 2 (Data Link Layer) of the OSI model and is used to discover information about directly connected Cisco devices. It helps network devices (such as routers, switches, and phones) exchange information such as device names, IP addresses, platform types, and more.

<b>Features:</b>\
Vendor-Specific: Only works on Cisco devices, although other vendors’ devices may support LLDP.

Layer 2 Protocol: Operates within the local network and does not pass through routers.

Used for Network Topology Discovery: Helps in identifying neighboring devices.

Automatic Discovery: Devices automatically send CDP packets every 60 seconds by default.

<b>Common Uses:</b>
Discovering neighboring Cisco devices.

Troubleshooting network connectivity.

Understanding network topology.

Automated configuration (in some cases).

<b>CDP Command:</b>
To view the CDP information on a Cisco device, the following command is used:
```

show cdp neighbors
```
This command provides a summary of directly connected Cisco devices, showing their device IDs, IP addresses, platform types, and interfaces.
```
show cdp neighbors detail
```
<b>This provides deeper information such as:</b>

+ Device ID

+ IP Address

+ Platform e.g. Cisco 2950

+ Capabilities e.g. router, switch

+ Interface Local interface of the device

## What is LLDP?
Link Layer Discovery Protocol (LLDP) is an open standard protocol, unlike CDP, which is proprietary. It is defined by the IEEE (Institute of Electrical and Electronics Engineers) in IEEE 802.1AB. LLDP operates at Layer 2 and is used to discover neighboring devices, regardless of vendor, allowing devices from different manufacturers to exchange topology information.

<b>Features:</b>\
Open Standard: LLDP works with devices from any vendor (e.g., Cisco, Juniper, HP).

Layer 2 Protocol: Like CDP, it operates at Layer 2 but is interoperable across vendor equipment.

Interoperability: It provides flexibility in multi-vendor environments.

Optional: LLDP is often disabled by default on many devices.

<b>Common Uses:</b>
Discovering neighboring devices in a multi-vendor environment.

Understanding network topology in non-Cisco environments.

Troubleshooting connections between devices of different manufacturers.

<b>LLDP Command:</b>
On Cisco devices, LLDP can be enabled with the following command:

```
lldp run
```
To view LLDP information on a Cisco device:
```
show lldp neighbors
```
For more detailed information:
```
show lldp neighbors detail
```
<b>This shows similar information as CDP but for devices that support LLDP, including:</b>

+ Chassis ID

+ Port ID

+ System Name

+ Capabilities (e.g., switch, router)

+ IP Address

### When to Use CDP vs LLDP:
Use CDP if your network is exclusively Cisco devices. It is designed to work seamlessly with Cisco hardware and provides rich information specific to Cisco devices.

Use LLDP if your network has multi-vendor devices or if you need an open standard for network discovery and troubleshooting. LLDP is more useful in non-Cisco environments or when you need a protocol that works across different vendors.

### Lab Scenario Example:
Setting Up CDP on a Cisco Router:

Verify if CDP is enabled:
```
show cdp
```
Enable CDP if it's not running:
```
cdp run
```
View neighbors:
```
show cdp neighbors
```
<b>Setting Up LLDP on a Cisco Switch:</b>

Verify LLDP status:

Enable LLDP if it's not running:
```
lldp run
```
View LLDP neighbors:
```
show lldp neighbors
```
Both protocols are important for network topology and troubleshooting, and the choice between CDP and LLDP depends on the equipment you are working with and the environment you manage.

### Key Differences between CDP and LLDP

| **Feature**              | **CDP (Cisco Discovery Protocol)**       | **LLDP (Link Layer Discovery Protocol)** |
|--------------------------|------------------------------------------|------------------------------------------|
| **Protocol Type**         | Proprietary (Cisco only)                 | Open Standard (IEEE 802.1AB)             |
| **Vendor Compatibility**  | Only Cisco devices                       | Multi-vendor (works with devices from any vendor) |
| **Layer**                 | Layer 2 (Data Link Layer)                | Layer 2 (Data Link Layer)               |
| **Interoperability**      | Limited to Cisco devices                 | Interoperable across multiple vendors   |
| **Default Behavior**      | Enabled by default on Cisco devices      | Disabled by default on many devices     |
| **Topology Discovery**    | Cisco devices only                       | Devices from any vendor                 |
| **Use in Mixed Environments** | Not useful in mixed vendor environments | Ideal for multi-vendor environments     |
| **Command to View Neighbors** | `show cdp neighbors`                  | `show lldp neighbors`                   |
| **Neighbor Information**  | Device ID, IP address, platform, capabilities | Chassis ID, Port ID, system name, capabilities |
| **Packet Interval**       | 60 seconds (default)                     | 30 seconds (default)                    |
| **Usage**                 | Primarily used in Cisco-only networks   | Used in multi-vendor environments       |
| **Network Management**    | Useful for Cisco-centric management and troubleshooting | Better suited for cross-platform network management |



### Key points to consider for the CCNA exam regarding CDP and LLDP:

<b>CDP and LLDP's Role in Network Topology:</b>

Both protocols help in network discovery, allowing you to map out your network devices and their interconnections.

CDP is a Cisco-only protocol, while LLDP is an open standard, so understanding both is important for multi-vendor environments.

<b>Default Settings and Commands:</b>

CDP is enabled by default on Cisco routers and switches, whereas LLDP needs to be manually enabled (lldp run command).

You will need to know the commands to check the status and view neighbors for both protocols.

<b>Commands for CCNA Exam:</b>

show cdp neighbors (For CDP neighbors)

show lldp neighbors (For LLDP neighbors)

show cdp / show lldp (To check the status of each protocol)

<b>Network Topology:</b>

For network troubleshooting, CDP and LLDP can help identify the physical topology of the network, as they provide information on neighboring devices (device type, IP address, interface, etc.).

CDP offers more specific Cisco-related details, whereas LLDP is more vendor-neutral and can work across different network brands.

<b>Network Protocols on CCNA Exam:</b>

Expect questions on how to enable and configure both protocols, as well as understanding their use cases.

Cisco-only features like CDP will be emphasized in a Cisco-dominant network, while LLDP is essential to know in mixed environments.

<b>Practical Understanding:</b>

Be prepared to troubleshoot network issues using CDP and LLDP for discovery and network verification.

You might be tested on whether you can enable these protocols, check device information, and configure them on Cisco switches/routers during the practical portions of the exam.