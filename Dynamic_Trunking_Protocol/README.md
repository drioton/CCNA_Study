# STP Switchport Mode 

##  General Rules for Switchport Mode Negotiation
 
<b>Access + Access</b> → Access (Fixed setting)\
<b>Trunk + Trunk</b> → Trunk (Fixed setting)\
<b>Access + Trunk</b> → Error (Incompatible)\
<b>Access + Dynamic Auto</b> → Access (Auto adapts to Access)\
<b>Access + Dynamic Desirable</b> → Access (Desirable wants trunk, but Access forces it to stay Access)\
<b>Trunk + Dynamic Auto</b> → Trunk (Auto adapts to Trunk)\
<b>Trunk + Dynamic Desirable</b> → Trunk (Desirable is happy because it got a trunk)\
<b>Dynamic Auto + Dynamic Auto </b>→ Access (Both are waiting, nobody initiates trunk)\
<b>Dynamic Auto + Dynamic Desirable</b> → Trunk (Desirable pushes for trunk, Auto agrees)\
<b>Dynamic Desirable + Dynamic Desirable</b> → Trunk (Both want trunk, so they get one)


**Key Concept**

<b>Desirable</b> always pushes for a trunk, but if the other side doesn’t agree (e.g., Access mode), it adapts.\
<b>Auto</b> only adapts, it never initiates anything.

## Configuration Examples

**1. Both ports in Access mode (No trunking)** 
+ SW1 & SW2 – Access mode (PC, printer, etc.
```
SW1(config)# interface FastEthernet0/1
SW1(config-if)# switchport mode access
SW1(config-if)# switchport access vlan 10
SW1(config-if)# no shutdown

SW2(config)# interface FastEthernet0/1
SW2(config-if)# switchport mode access
SW2(config-if)# switchport access vlan 10
SW2(config-if)# no shutdown
```
+  **Result:** Ports are in **Access mode**, carrying only **VLAN 10**.

**2. One port Access, the other Dynamic Auto (No trunking)** 
+ SW1 – Access, SW2 – Dynamic Auto
```
SW1(config)# interface FastEthernet0/1
SW1(config-if)# switchport mode access
SW1(config-if)# switchport access vlan 10
SW1(config-if)# no shutdown

SW2(config)# interface FastEthernet0/1
SW2(config-if)# switchport mode dynamic auto
SW2(config-if)# no shutdown
```
+ **Result:** Access mode – Dynamic Auto waits, but Access does not negotiate trunking.

**3. One port Dynamic Auto, the other Dynamic Desirable (Trunk is formed)** 
+ SW1 – Dynamic Auto, SW2 – Dynamic Desirable
```
SW1(config)# interface FastEthernet0/1
SW1(config-if)# switchport mode dynamic auto
SW1(config-if)# no shutdown

SW2(config)# interface FastEthernet0/1
SW2(config-if)# switchport mode dynamic desirable
SW2(config-if)# no shutdown
```
+ Result: Trunk mode – Desirable convinces Auto to form a trunk.

**4. Both ports in Dynamic Desirable mode (Trunk is formed)** 
+ SW1 & SW2 – Dynamic Desirable
```
SW1(config)# interface FastEthernet0/1
SW1(config-if)# switchport mode dynamic desirable
SW1(config-if)# no shutdown

SW2(config)# interface FastEthernet0/1
SW2(config-if)# switchport mode dynamic desirable
SW2(config-if)# no shutdown
```
+ **Result: Trunk mode** – Both ports want a trunk, so they negotiate it successfully.

**5. One port Dynamic Auto, the other Trunk (Trunk is formed)** 
+ SW1 – Dynamic Auto, SW2 – Trunk
```
SW1(config)# interface FastEthernet0/1
SW1(config-if)# switchport mode dynamic auto
SW1(config-if)# no shutdown

SW2(config)# interface FastEthernet0/1
SW2(config-if)# switchport mode trunk
SW2(config-if)# no shutdown
```
+ **Result: Trunk mode** – Trunk is enforced.

**6. Both ports in Trunk mode (Fixed trunk, ignores DTP negotiation)** 
+ SW1 & SW2 – Trunk mode
```
SW1(config)# interface FastEthernet0/1
SW1(config-if)# switchport mode trunk
SW1(config-if)# switchport trunk allowed vlan 10,20,30
SW1(config-if)# no shutdown

SW2(config)# interface FastEthernet0/1
SW2(config-if)# switchport mode trunk
SW2(config-if)# switchport trunk allowed vlan 10,20,30
SW2(config-if)# no shutdown
```
+ **Result: Trunk mode** – Only VLANs 10, 20, and 30 are transmitted.

# Dynamic Trunking Protocol (DTP) Guide

## What is DTP?
Dynamic Trunking Protocol (DTP) is a Cisco proprietary protocol used to automatically negotiate trunk links between switches. By default, some Cisco switches have DTP enabled, which means interfaces can dynamically form trunk connections based on their mode settings.

### Why is DTP important?
- It allows switches to dynamically form trunk links without manual configuration.
- Reduces the need for static trunk configurations.
- Can create security risks if not controlled properly.

---

## DTP Mode Negotiation Table

| Port 1 Configuration          | Port 2 Configuration          | Resulting Mode                     |
|--------------------------------|------------------------------|------------------------------------|
| `switchport mode access`       | `switchport mode access`       | **Access**                        |
| `switchport mode access`       | `switchport mode dynamic auto` | **Access**                        |
| `switchport mode access`       | `switchport mode dynamic desirable` | **Access**                        |
| `switchport mode access`       | `switchport mode trunk`       | **Error (Incompatible)**          |
| `switchport mode dynamic auto` | `switchport mode dynamic auto` | **Access** (Neither initiates trunk) |
| `switchport mode dynamic auto` | `switchport mode dynamic desirable` | **Trunk** (Desirable initiates trunk) |
| `switchport mode dynamic auto` | `switchport mode trunk`       | **Trunk**                         |
| `switchport mode dynamic desirable` | `switchport mode dynamic desirable` | **Trunk**                         |
| `switchport mode dynamic desirable` | `switchport mode trunk` | **Trunk**                         |
| `switchport mode trunk`       | `switchport mode trunk`       | **Trunk**                         |



## How to Disable DTP (Security Best Practice)
DTP can be a security risk because it allows trunking to be negotiated dynamically. To disable DTP on a port, use:

```sh
switchport nonegotiate
```
This prevents the port from sending or responding to DTP messages.

Use this command on **static access ports** and **manually configured trunks** to avoid unwanted trunk formation.

Example:
```sh
SW1(config)# interface FastEthernet0/1
SW1(config-if)# switchport mode trunk
SW1(config-if)# switchport nonegotiate
SW1(config-if)# no shutdown
```
 Result: The interface is a trunk, but it will not negotiate with the other side.



## When to Use Each Mode

| Mode                    | When to Use It |
|-------------------------|---------------|
| `access`               | End devices (PCs, printers, etc.) |
| `trunk`                | Between switches, routers, servers needing VLAN tagging |
| `dynamic auto`         | When unsure if a trunk is needed (default, but not recommended) |
| `dynamic desirable`    | When you want to form a trunk if the other side allows it |



## Common Issues & Troubleshooting

###  Trunk Not Forming?
**Possible Causes:**
1. One side is `access`, while the other is `trunk` – Access mode does not negotiate trunking.
2. Both sides are `dynamic auto` – Auto mode does not initiate trunking.
3. DTP is disabled with `switchport nonegotiate`.

**Fix:**
- Ensure at least one side is `dynamic desirable` or both are `trunk`.



## Summary
- DTP allows dynamic trunk negotiation between switches.
- It can be disabled for security (`switchport nonegotiate`).
- `dynamic auto + dynamic auto` results in an **Access mode** (no trunk).
- `dynamic auto + dynamic desirable` results in **Trunk mode**.
- `access + trunk` causes an **Error**.



# Spanning Tree Protocol (STP) - Show Commands

This document contains useful **show** commands for troubleshooting and managing **Spanning Tree Protocol (STP)** on Cisco switches.



##  Basic STP Commands

```sh
show spanning-tree                # Displays overall STP status on the switch
show spanning-tree vlan <VLAN_ID> # Displays STP for a specific VLAN
show spanning-tree summary        # Provides a brief STP summary
show spanning-tree active         # Shows only VLANs where STP is active
```


##  Detailed STP Information

```sh
show spanning-tree root           # Displays root bridge information
show spanning-tree interface <interface> detail # Shows detailed STP info for a port
show spanning-tree bridge         # Displays this switch’s Bridge ID
show spanning-tree vlan <VLAN_ID> detail # Detailed STP info for a specific VLAN
```



## Troubleshooting & Diagnostics

```sh
show spanning-tree blockedports   # Displays ports blocked by STP
show spanning-tree inconsistentports # Shows ports with STP issues (Root Guard, BPDU Guard)
show spanning-tree mst configuration # Displays MSTP configuration
show spanning-tree mst detail      # Provides detailed MSTP info
show spanning-tree rapid-pvst      # Shows the status of RSTP
```



##  Notes:
- Use **`show spanning-tree`** to get a general overview of the switch’s STP state.
- The **`detail`** option provides in-depth information for specific interfaces or VLANs.
- Always check **blocked ports** and **inconsistent ports** when troubleshooting STP issues.


























