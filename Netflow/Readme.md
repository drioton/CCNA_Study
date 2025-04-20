# NetFlow – Monitoring Network Traffic 
 (For CCNA, you only need to understand NetFlow v5/v9, basic export, and interpretation.)
 + What is NetFlow?
NetFlow is a Cisco protocol that collects metadata about IP traffic as it enters a router or switch.

It answers:

 Who is talking to whom?

 How much data are they sending?

 When did it happen and through which interface?

 <b>NetFlow Components</b>

| Component        | Role / Description                                     |
|------------------|--------------------------------------------------------|
| Flow             | A unidirectional set of packets with common attributes |
| Exporter         | The device collecting and sending flow data            |
| Collector        | Server receiving and analyzing flow data               |
| Analyzer         | GUI or software that visualizes flow info              |

In simple CCNA terms:
Cisco router = Exporter\
Tool like SolarWinds, nProbe, PRTG = Collector/Analyzer

### What is a “Flow”?
A flow is defined by this 5-tuple:


- Source IP address
- Destination IP address
- Source port
- Destination port
- Layer 3 protocol
(TCP/UDP/ICMP)
 NetFlow Basic Configuration (Cisco IOS)
```
enable
configure terminal
```
+ Enable NetFlow on interface
```
interface GigabitEthernet0/1
 ip flow ingress
 ip flow egress
```
+ Set NetFlow version and destination (collector IP)
```
ip flow-export destination 192.168.1.200 9996
ip flow-export version 9
ip flow-export source GigabitEthernet0/1
ip flow-cache timeout active 60
end
write memory
```


 Real Use Case

| Scenario                | What NetFlow can tell you                        |
|-------------------------|--------------------------------------------------|
| User hogging bandwidth | See top talkers by IP                            |
| DDoS detection          | Sudden spikes in similar flows (same dest IP)   |
| Malware beaconing       | Repeated flows to strange external IPs          |

### Why NetFlow Is Useful
<b>Visibility:</b> See exactly what traffic is flowing where

<b>Security: </b>Spot anomalies (sudden surges, strange patterns)

<b>Troubleshooting:</b> Understand performance issues, congestion

 Mini-Lab Setup (Concept)

Devices:
- Cisco Router with NetFlow enabled
- PC or VM running a NetFlow collector (nTop, SolarWinds, PRTG)
- Test clients generating traffic (e.g., ping, iperf)

Goal:
- Send traffic through router
- Capture flow data on the collector
- Analyze what flows were generated
You can test NetFlow collectors on a local VM or use Wireshark with plugins to decode flow packets.

### Summary for CCNA
NetFlow tracks metadata about IP traffic

You configure ingress/egress on interfaces

Flows are sent to a collector server

NetFlow is used for monitoring, security, and troubleshooting

You must know basic config and purpose – not deep internals

 ## NetFlow Lab – Basic Setup (GitHub Format)

Capture and export flow data from a Cisco router to a NetFlow collector for traffic analysis.

 + Lab Topology Diagram
```
+------------+       Fa0/0         +-------------+       UDP/2055       +-------------------+
|   PC1      |---------------------|   R1        |----------------------| NetFlow Collector |
| 10.1.1.10  |                     | 10.1.1.1    |                      |                   |
+------------+                     +-------------+                      +-------------------+
```
+ Device Configuration
Router R1 Configuration (Cisco IOS)

+  Define the interface that receives/sends NetFlow traffic
```
interface FastEthernet0/0
 ip address 10.1.1.1 255.255.255.0
 ip flow ingress
 ip flow egress
```

+  Enable NetFlow on the router
```ip flow-export destination 10.1.1.100 2055
ip flow-export version 9
ip flow-export source FastEthernet0/0
ip flow-cache timeout active 
```
+ Explanation:

ip flow ingress/egress: Captures both incoming and outgoing flows.

ip flow-export destination: Specifies where to send NetFlow data (collector).

ip flow-export version 9: Most flexible format for exporting.

ip flow-cache timeout active 1: Flow cache active timeout in minutes.

 NetFlow Collector
 
### Use any of the following:

SolarWinds NetFlow Traffic Analyzer

ntopng

Wireshark (limited)

Flowalyzer (free for testing)

Make sure the collector is listening on UDP port 2055.

### Testing the Flow
From PC1, ping or generate traffic to the router or another host.

Check flow stats on the router:

```
show ip cache flow
show flow-export
```
+ Verification Commands
```
show ip flow interface
show ip flow export
show ip cache flow
debug ip flow
```
+ Use Cases
Monitor bandwidth per IP/protocol.

Detect DDoS or port scanning patterns.

Baseline network behavior for capacity planning.

Combine with SNMP/Syslog for full visibility.

