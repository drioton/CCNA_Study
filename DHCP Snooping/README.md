# DHCP Snooping + Dynamic ARP Inspection (DAI)

## Concepts
<b>DHCP Snooping</b> is a Layer 2 security feature that filters untrusted DHCP messages and builds a binding table of valid IP-to-MAC-to-port associations.

<b>DAI (Dynamic ARP Inspection)</b> uses that DHCP Snooping table to validate ARP packets and block spoofed ARP replies.

<b>Trust/Untrust:</b> Interfaces must be manually classified as trusted (e.g., facing a real DHCP server) or untrusted (facing clients).

### <b>Attack Prevention:</b>

Prevents DHCP Spoofing (rogue DHCP servers giving bad IP info).

Prevents DHCP Starvation (flooding DHCP server with requests).

Prevents ARP Spoofing/Poisoning (MITM attacks using false MAC-IP maps).

### DHCP Snooping: Key Terms
<b>Trusted Port:</b> Interface where valid DHCP responses (e.g., from real servers) are allowed.

<b>Untrusted Port:</b> Interface where DHCP replies are blocked (prevents rogue DHCP).

<b>Snooping Binding Table:</b> Automatically built from real DHCP transactions: stores MAC, IP, VLAN, interface, lease time.

### DAI: Dynamic ARP Inspection
+ Relies on DHCP Snooping database.

+ Compares incoming ARP replies against known bindings.

+ Blocks ARP packets with wrong MAC/IP combos.

+ DAI requires DHCP Snooping to be enabled and working.

### Configuration Example: DHCP Snooping + DAI
```
# Enable DHCP Snooping globally
Switch(config)# ip dhcp snooping

# Enable for specific VLAN(s)
Switch(config)# ip dhcp snooping vlan 10

# Mark the port towards the DHCP server as trusted
Switch(config-if)# interface GigabitEthernet0/1
Switch(config-if)# ip dhcp snooping trust

# Set rate limit for DHCP messages (against starvation)
Switch(config-if)# ip dhcp snooping limit rate 10

# Enable Dynamic ARP Inspection
Switch(config)# ip arp inspection vlan 10

# Trust DHCP server port for ARP too
Switch(config-if)# interface GigabitEthernet0/1
Switch(config-if)# ip arp inspection trust
```

### Attack Scenarios Prevented

+ Rogue DHCP Server (DHCP Spoofing)

+ DHCP Starvation (DoS using fake DHCP requests)

+ ARP Spoofing (Man-in-the-middle using fake MAC addresses)

+ Gratuitous ARP Poisoning

### DHCP Snooping/DAI Summary Table

| Feature               | DHCP Snooping                              | Dynamic ARP Inspection (DAI)                   |
|-----------------------|---------------------------------------------|------------------------------------------------|
| **Layer**             | Layer 2                                     | Layer 2                                        |
| **Purpose**           | Prevent rogue DHCP servers & starvation     | Prevent ARP spoofing/poisoning                 |
| **Depends On**        | Switchport trust, VLANs                     | DHCP Snooping binding table                    |
| **Validates**         | DHCP Offers, Acks, Requests                 | ARP replies and requests                        |
| **Trust Required**    | Yes (DHCP server port)                      | Yes (for ARP inspection trust ports)           |
| **Binding Table**     | Yes (IP ↔ MAC ↔ Port)                       | Yes (reads from DHCP Snooping)                 |
| **Rate-Limiting**     | Yes (DHCP packets per second)               | Optional                                        |
| **Attack Protection** | Rogue server, starvation, spoofing          | ARP poisoning, MITM                            |

Static IP clients won’t appear in DHCP snooping binding table – use manual ARP entries or IP Source Guard exceptions.

Combine with Port Security, Private VLANs, and ACLs for layered defense.

DHCP Snooping + DAI is most effective on access layer switches.

Use Syslog to monitor violations for learning or alerts.

#  IP Source Guard (IPSG) – Concept + Mini-Lab

### What is IP Source Guard?
IP Source Guard is a Layer 2 security feature that blocks IP spoofing by verifying the source IP address of incoming packets against a DHCP Snooping Binding Table or static binding.

If the IP/MAC doesn’t match what’s expected for that port → the packet is dropped.

### How It Works
Tied to DHCP Snooping: uses the IP ↔ MAC ↔ Port info from the binding table.

If a device sends a packet with an IP not assigned to its port → blocked.

Can also work with static IPs by manually configuring bindings.

### Protection Against
IP Spoofing (attacker fakes IP address of another host)

Man-in-the-middle (MITM) on local network

Bypassing access controls by injecting forged packets

### Configuration Example
 Enable DHCP Snooping
```
Switch(config)# ip dhcp snooping
Switch(config)# ip dhcp snooping vlan 10
```

 Enable IP Source Guard on access port
```
Switch(config-if)# interface FastEthernet0/2
Switch(config-if)# ip verify source
```
Optional: to include MAC check as well:
```
Switch(config-if)# ip verify source port-security
```
### Mini-Lab Scenario: Secure VLAN 10 Access


**Topology**
[PC1] --- Fa0/2 --- [SW1] --- Fa0/1 --- [DHCP Server]

VLAN 10

<b>Goal:</b>
Only allow legitimate DHCP-assigned IPs to send traffic on Fa0/2. Block any spoofed packets or rogue clients.

<b>Steps:</b>
Configure VLAN 10.

Enable DHCP Snooping on VLAN 10.

Set Fa0/1 (toward DHCP server) as trusted.

Enable IP Source Guard on Fa0/2 (PC1).

<b>Test:</b>

Legit PC gets IP → passes traffic 

Attacker plugs in with static IP → traffic dropped 

###  Attack Diagram
The switch sees that the attacker’s port never got 192.168.1.5 from DHCP → drops packets instantly.

                  +-------------+
                  |  Attacker   |
                  |  IP: 192.168.1.5 (spoofed) |
                  +------+------+ 
                         |
              (Untrusted Port Fa0/3)
                         |
       +-----------------+----------------+
       |                SW1              |
       |  DHCP Snooping + IP Source Guard|
       +----------------+----------------+
                         |
             (Trusted Port Fa0/1)
                         |
                  +-------------+
                  | DHCP Server |
                  +-------------+
###  Important 
Works only on Layer 2 access ports (not routed interfaces).

Without DHCP Snooping, IPSG has no source data to validate.

<b>Combine with:</b>

Port Security – control MAC addresses

DAI – protect ARP

ACLs – restrict traffic

For static IPs you must configure static bindings:

```
Switch(config)# ip source binding 0011.2233.4455 vlan 10 192.168.1.10 interface FastEthernet0/2
```