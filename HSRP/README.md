# First Hop Redundancy Protocols (FHRP)

### Purpose of FHRP
All FHRP protocols provide a virtual default gateway IP to hosts in a subnet.
If the real gateway fails, another router takes over seamlessly.


### Comparison Table

| Feature          | HSRP                        | VRRP                         | GLBP                            |
|------------------|-----------------------------|-------------------------------|----------------------------------|
| Full Name        | Hot Standby Router Protocol | Virtual Router Redundancy Protocol | Gateway Load Balancing Protocol |
| Vendor           | Cisco only                  | Open standard (RFC 5798)      | Cisco only                      |
| Virtual IP       | Configured manually         | Usually highest real IP       | Configured manually             |
| Virtual MAC      | 0000.0C07.ACxx (v1)         | 0000.5E00.01xx                | 0007.b4xx.xxxx                   |
| Load Balancing   | ❌ No                       | ❌ No                         | ✅ Yes                           |
| Preemption       | Manual (`preempt`)          | Enabled by default            | Enabled by default              |
| Election         | Priority > Highest IP       | Priority > Highest IP         | Priority > Highest IP (AVG)     |
| Timers (default) | Hello 3s / Hold 10s         | Advertisement 1s / Master Down 3s | Hello 3s / Hold 10s        |
| Versions         | v1 (IPv4) / v2 (IPv4 + IPv6)| v2 (IPv4 + IPv6)              | Single version (IPv4 only)      |


## HSRP — Cisco Proprietary
### States
Initial → Learn → Listen → Speak → Standby → Active

### HSRP State Machine Breakdown
HSRP routers move through six states. Each state defines the router's role and its interaction with others.

<b>1. Initial</b>
- Meaning:
Router just powered on or HSRP just enabled.\
- Action:
Interface not yet participating in HSRP. No timers, no messages sent or received.

<b>2. Learn</b>
- Meaning:
Router doesn't know the virtual IP yet.

- Action:
Learns the virtual IP from HSRP hello messages from active router.
If you configure the virtual IP manually, this state is skipped.

<b>3. Listen</b>
- Meaning:
Router knows the virtual IP but is not active or standby.

- Action:
Just listening to HSRP hello messages from active and standby routers. Ready to promote if needed.

<b>4. Speak</b>
- Meaning:
Router participates in HSRP elections.

- Action:
Sends hello messages, may claim active/standby role based on priority.

- Timer Action:
Starts election process if no active router is heard.

<b>5. Standby</b>
- Meaning:
The backup router – ready to take over if active fails.

- Action:
Listens for hello messages from active router. If they stop, it waits for Hold Time to expire, then takes over.

- Key Point:
Only one standby router per group in HSRP.

<b>6. Active</b>
- Meaning:
This router is currently handling traffic for the virtual IP.

- Action:
Sends hello messages (default every 3s), owns the virtual MAC/IP, responds to ARP.

| State    | Role                           | Key Action                                       |
|----------|--------------------------------|--------------------------------------------------|
| Initial  | Not participating              | Waiting for HSRP to be enabled                   |
| Learn    | Waiting for virtual IP         | Learns virtual IP via hello messages            |
| Listen   | Passive member                 | Monitors hello messages, not standby or active  |
| Speak    | Election in progress           | Sends hello messages, competes for roles        |
| Standby  | Backup router                  | Ready to become active if needed                |
| Active   | Main gateway                   | Forwards traffic, sends hellos, owns VIP/MAC    |

### Notes for CCNA:
- Timers: Default Hello = 3s, Hold = 10s

- Preemption: Must be configured to allow router with higher priority to take over

- State transitions can be observed via:
```
show standby
debug standby
```
### Election Logic
Highest priority wins (default 100)

If tie → highest IP wins

<b>Preemption must be enabled</b> to take over

### Virtual MAC (v1)
Format: 0000.0C07.ACxx

xx = group number in hex

Example: Group 1 = 0000.0C07.AC01

### HSRP v2
Supports IPv6

Up to 4096 groups

MAC format: 0000.0C9F.Fxxx

### Key Commands
```
standby 1 ip 192.168.1.254
standby 1 priority 110
standby 1 preempt
standby 1 timers 1 3
standby 1 track GigabitEthernet0/1 20
```
## VRRP — Open Standard
<b>Election Logic</b>
Highest priority wins (default 100)

If tie → highest real IP wins

Preemption is enabled by default

### Virtual IP
Can be same as real IP of master

Optional config: vrrp 1 ip 192.168.1.254

### Virtual MAC
Format: 0000.5E00.01xx

xx = group ID (1–255)

### Timers
Advertisement: 1s (default)

Master Down = 3 × Advertisement + Skew

### Key Config
```
vrrp 1 ip 192.168.1.254
vrrp 1 priority 110
vrrp 1 preempt
```
## GLBP — Cisco Proprietary with Load Balancing
### How it Works
Multiple routers can forward traffic

One router is AVG (Active Virtual Gateway) → assigns virtual MACs

Others are AVFs (Active Virtual Forwarders) → forward traffic

### Load Balancing Methods
Round-robin

Weighted

Host-dependent

### Virtual MACs
Format: 0007.b4xx.xxxx

Multiple MACs per group

### Key Config
```
glbp 1 ip 192.168.1.254
glbp 1 priority 110
glbp 1 preempt
glbp 1 load-balancing round-robin
```
## Interface Tracking (HSRP/GLBP)
Reduces priority if tracked interface fails. Useful for detecting WAN failure.
```
standby 1 track GigabitEthernet0/1 20
glbp 1 track GigabitEthernet0/1 decrement 20

```
## Preemption Summary

| Protocol | Preempt by Default? | Command Needed?     |
| -------- | ------------------- | ------------------- |
| HSRP     |  No                 | `standby 1 preempt` |
| VRRP     |  Yes                | Optional            |
| GLBP     |  Yes                | Optional            |


## Show Commands for Troubleshooting
```
show standby
show vrrp
show glbp
show standby brief
debug standby
debug glbp
```

## Summary by Use Case

| Goal                      | Protocol |
| ------------------------- | -------- |
| Simple failover (Cisco)   | HSRP     |
| Open standard needed      | VRRP     |
| Load balancing + failover | GLBP     |

### FHRP Real-World Scenario Questions

1. A network has two Cisco routers using HSRP. The backup router never becomes active, even after the active router goes down. Why?
Answer:
Preemption is not enabled on the backup router (standby 1 preempt). Without it, even if it has higher priority, it won’t take over.

2. You configure HSRP with the same priority (100) on both routers. Which one becomes active?
Answer:
The router with the higher IP address on the HSRP-enabled interface becomes active (tie-breaker logic).

3. You’re using VRRP with no preempt configuration. The backup router takes over when the master fails. When the master comes back, it becomes master again. Why?
Answer:
Because preemption is enabled by default in VRRP. The higher-priority router will always take over when it returns.

4. A host has 3 routers configured as gateways. Sometimes it uses R1, sometimes R2, sometimes R3. What's happening?
Answer:
This is likely GLBP in action. The host is getting different virtual MACs assigned by the AVG (load balancing).

5. Your GLBP AVG fails. What happens?
Answer:
One of the AVFs is elected as the new AVG based on priority. The virtual gateway remains available.

6. You track an interface in HSRP and set decrement 20. Your priority is 110. The tracked interface goes down. What’s your new priority?
Answer:
Priority becomes 90 (110 – 20). If another router has a higher priority and preempt is enabled, it becomes active.

7. Users report intermittent gateway issues after failover. You check show standby, and the timers are Hello 3s, Hold 10s. What could be optimized?
Answer:
Lower the timers to speed up failover:
```
standby 1 timers 1 3
```
This reduces the detection time from 10s to 3s.

8. You’re setting up VRRP. You don’t define a virtual IP. The routers still elect a master. How is that possible?
Answer:
In VRRP, if you don’t define a virtual IP, it will use the highest real IP address among the participating routers.

9. Why would you choose GLBP over HSRP in a large network?
Answer:
Because GLBP supports load balancing, not just failover. It improves bandwidth utilization across multiple routers.

10. You see a MAC address 0000.0C07.AC05 in a switch’s MAC table. What does this tell you?
Answer:
This is a HSRP virtual MAC address for group 5. It tells you HSRP is running on that subnet.






