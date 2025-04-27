# Definitions

### Bandwidth

Bandwidth is the maximum amount of data that can be transmitted over a network link in a given amount of time.\
It is usually measured in bits per second (bps), kilobits per second (Kbps), megabits per second (Mbps), or gigabits per
second (Gbps).\
In networking, bandwidth is not how much data you have, but how fast you can move data from one place to another.\
Higher bandwidth means more data can be transmitted at once.

### Delay

Delay is the time it takes for a packet to travel from the sender to the receiver. It is usually measured in
milliseconds (ms).

-   Delay can be caused by several factors:

<b>Propagation delay:</b> time it takes for data to travel through the physical medium (like fiber optic cable).

<b>Serialization delay:</b> time needed to put the bits onto the link.

<b>Queuing delay:</b> time a packet waits in a queue before being transmitted.

<b>Processing delay:</b> time needed for a device to examine and forward the packet.

### Jitter

Jitter is the variation in delay of received packets. If one packet arrives in 10 milliseconds and the next in 30
milliseconds, the jitter is 20 milliseconds.\
Jitter is very important for real-time traffic like voice and video because it can cause choppy audio or video freezing.

### Loss

Loss is when packets are dropped and do not reach the destination. Packet loss can happen because of congestion, link
errors, or hardware failures. Some applications like file transfers (FTP, HTTP) can recover from packet loss because TCP
will retransmit the missing packets. Other applications like voice and video cannot recover easily from packet loss and
will experience poor quality.

## How different types of traffic react to these parameters:

<b>Data traffic (HTTP, FTP, email):</b>

Bandwidth: not very sensitive, but low bandwidth will slow down performance.

Delay: can tolerate moderate delay.

Jitter: not sensitive at all.

Loss: very sensitive if using TCP. Packet loss will cause retransmissions and slow down the session.

<b>Voice traffic (VoIP):</b>

Bandwidth: needs very little bandwidth per call (about 64 kbps with overhead).

Delay: very sensitive. Delay should be less than 150 milliseconds one-way.

Jitter: very sensitive. High jitter causes poor audio quality.

Loss: very sensitive. More than 1 percent loss leads to poor call quality.

<b>Video traffic (Streaming, Video conferencing):</b>

Bandwidth: needs high bandwidth, depending on video quality.

Delay: sensitive, but more tolerant than voice.

Jitter: sensitive. Causes video frames to arrive late and leads to freezing or artifacts.

Loss: somewhat tolerant. Some modern video protocols can recover from minor packet loss.

## Classification, Marking, Queuing, and Policing:

<b>Classification:</b>

The network device identifies and separates different types of traffic.

It uses parameters like IP address, port number, or protocol type.

Example: traffic going to UDP port 5060 can be classified as VoIP signaling.

<b>Marking:</b>

After classification, the packet is tagged with a specific value.

In IP networks, marking is usually done using DSCP (Differentiated Services Code Point) field in the IP header.

Example: voice traffic can be marked with DSCP EF (Expedited Forwarding).

<b>Queuing:</b>

When congestion happens, routers and switches decide the order of sending packets.

Important traffic (like voice) is placed in higher priority queues.

Lower priority traffic (like file downloads) can be delayed or dropped if necessary.

Cisco uses different queuing mechanisms like FIFO (First In First Out), WFQ (Weighted Fair Queuing), and LLQ (Low
Latency Queuing).

<b>Policing:</b>

Policing limits the traffic rate. If traffic exceeds the configured rate, packets can be dropped or remarked to a lower
priority.

Policing is often used on the network edge to prevent one customer or application from using too much bandwidth.

## Recognizing DiffServ and knowing basic DSCP values:

-   DiffServ (Differentiated Services):

DiffServ is a QoS model where packets are classified and marked at the network edge.

Core routers do not inspect every packet deeply; they just look at the DSCP marking and forward accordingly.

This makes DiffServ scalable and efficient for large networks.

-   Common DSCP values:

EF (Expedited Forwarding): DSCP 46

Used for voice traffic that needs the highest priority and low delay.

<b>AF (Assured Forwarding):</b>

AF41 (DSCP 34), AF42 (DSCP 36), AF43 (DSCP 38)

Used for video traffic or important business data.

AF21 (DSCP 18), AF22 (DSCP 20), AF23 (DSCP 22)

Used for lower-priority but still important data.

<b>CS (Class Selector):</b>

CS0 (DSCP 0): Default, best effort traffic.

CS1, CS2, CS3, CS4, CS5, CS6, CS7: Different priority classes.

DSCP is 6 bits in the IP header and can represent 64 different classes of service.

## Basic Cisco QoS Steps

### Step 1: Classification

Classification is the process of identifying and separating different types of traffic.

The router or switch examines the incoming packet headers to determine which traffic class it belongs to.

<b>Common fields used for classification include:</b>

Source and destination IP address

TCP or UDP port number

Protocol type (for example, ICMP, TCP, UDP)

VLAN ID

Incoming interface

Classification is performed using class-maps in Cisco IOS.

Without classification, the network would treat all traffic equally, which is not acceptable for critical applications.

Example: class-map match-any VOICE match ip dscp ef

### Step 2: Marking

Marking means adding a specific tag or label to packets after they have been classified.

Marking allows network devices downstream to know how to handle these packets without having to inspect them again.

<b>The most common marking methods are:</b>

DSCP (Differentiated Services Code Point) marking in the IP header

CoS (Class of Service) marking at Layer 2 in Ethernet frames

Marking is performed using policy-maps and set actions.

Example: policy-map MARK_VOICE class VOICE set dscp ef

### Step 3: Queuing

Queuing determines how packets are buffered and scheduled for transmission when there is congestion.

When the interface is not congested, all packets are transmitted immediately.

When congestion occurs, queuing policies decide which packets to send first and which packets to delay or drop.

<b>Different queuing mechanisms include></b> FIFO (First In, First Out): No QoS. Packets are transmitted in the order
they arrive.

WFQ (Weighted Fair Queuing): Traffic flows get fair access based on their weight.

CBWFQ (Class-Based Weighted Fair Queuing): Administrators define classes and allocate bandwidth to each class.

LLQ (Low Latency Queuing): Adds strict priority queue to CBWFQ for real-time traffic like voice.

Example: policy-map QOS_POLICY class VOICE priority percent 30 class class-default fair-queue

<b>Explanation:</b>

The priority command creates a strict priority queue for voice traffic.

Voice packets are sent first during congestion.

Other traffic is queued fairly.

### Step 4: Shaping and Policing

<b>Shaping:</b>

Traffic shaping smooths traffic by buffering excess packets and sending them later.

Shaping is a "gentle" way to enforce bandwidth limits.

Used mainly on outbound interfaces.

Shaping is a good option when the network can handle some delay.

Example: policy-map SHAPE_POLICY class class-default shape average 1000000

<b>Explanation:</b>

Shapes traffic to 1 Mbps by buffering bursts.

<b>Policing:</b>

Traffic policing enforces bandwidth limits strictly.

If traffic exceeds the limit, packets are either dropped or remarked.

Used to protect the network from excessive traffic or to enforce service agreements.

Example: policy-map POLICE_POLICY class VOICE police 64000 conform-action transmit exceed-action drop

<b>Explanation:</b>

Voice traffic is limited to 64 Kbps.

Packets within the limit are transmitted.

Packets exceeding the limit are dropped.

### Summary:

Classification: Identify the traffic type.

Marking: Tag the traffic so other devices recognize its importance.

Queuing: Prioritize and buffer traffic when congestion happens.

Shaping/Policing: Control traffic rate to avoid congestion or enforce limits.

## Cisco QoS Configuration Examples and Commands

1. Define Class-Maps (Classification) A class-map is used to define the criteria for traffic classification.

Example 1: Classify VoIP traffic based on DSCP EF.

```
class-map match-any VOICE
 match ip dscp ef
```

Example 2: Classify Video traffic based on DSCP AF41.

```
class-map match-any VIDEO
 match ip dscp af41
```

Example 3: Classify default (all other traffic).

```
class-map match-any DEFAULT
 match any
```

2. Define Policy-Maps (Marking, Queuing, Policing) A policy-map is used to apply actions to classified traffic.

Example: Marking and Queuing with Priority for Voice.

```
policy-map QOS_POLICY
 class VOICE
  priority percent 30
 class VIDEO
  bandwidth percent 20
 class DEFAULT
  fair-queue
```

Explanation:

priority percent 30 gives Voice strict priority up to 30% of the bandwidth.

bandwidth percent 20 guarantees 20% for Video.

fair-queue means default traffic shares the leftover bandwidth fairly.

Example: Policing VoIP traffic to 64 Kbps.

```
policy-map POLICE_VOICE
 class VOICE
  police 64000 conform-action transmit exceed-action drop
```

3. Apply Service Policy to Interface (Activation) After creating class-maps and policy-maps, you must apply the
   service-policy to an interface.

Example: Apply outbound QoS to an interface. `interface GigabitEthernet0/1  service-policy output QOS_POLICY ` Example:
Apply inbound policing to an interface.

```
interface GigabitEthernet0/1
 service-policy input POLICE_VOICE
```

4. Basic Shaping Example Shape all traffic to 1 Mbps.

```
policy-map SHAPE_ALL
 class class-default
  shape average 1000000
```

Apply it to an interface:

```
interface GigabitEthernet0/1
 service-policy output SHAPE_ALL
```

5. Example of Trusting DSCP on a Switchport If the endpoint device already marks DSCP correctly (like an IP phone), you
   can trust it.

```
interface GigabitEthernet1/0/10
 mls qos trust dscp
```

On some switches you need to enable QoS globally first:

```
mls qos
```

6. DSCP Marking at the Access Layer (Rewrite Markings) If you do not trust the end device, you can overwrite DSCP:

```
policy-map MARK_TRAFFIC
 class VOICE
  set dscp ef
 class VIDEO
  set dscp af41
 class DEFAULT
  set dscp default
```

Apply this policy inbound:

```
interface GigabitEthernet1/0/10
 service-policy input MARK_TRAFFIC
```

### Important Notes:

priority creates a strict priority queue, meaning traffic in that class is sent first during congestion.

bandwidth guarantees minimum bandwidth but does not prioritize traffic.

fair-queue automatically shares bandwidth across flows.

shape buffers packets when exceeding allowed rate; slower but no drops.

police drops or re-marks excess packets immediately.

Always classify and mark traffic close to the source (at the access layer).

Core routers and distribution layers usually only queue based on markings, they do not classify again.

## Cisco QoS - Deep Dive

### Classification 

Classification identifies the type of traffic.

<b>Traffic can be classified by:</b>

-   Access Control Lists (ACLs)

-   NBAR (Network-Based Application Recognition)

-   Source/Destination IP, Port, Protocol

-   VLAN, Interface

<b>Purpose:</b> Know what kind of packet you are handling (voice, video, bulk data).

Example:
```
class-map match-any VOICE
 match ip dscp ef
```

Main marking fields:

Layer 2: CoS (Class of Service) in Ethernet frames (802.1p)

Layer 3: IP Precedence or DSCP (Differentiated Services Code Point)

### Marking

Marking helps downstream devices prioritize without needing to inspect packets again.

Example:
```
policy-map MARK_POLICY
 class VOICE
  set dscp ef
```

DSCP EF (Expedited Forwarding) = 46 (decimal) = Used
for Voice.

### Queuing 

Queuing manages packet sending when there is congestion.

<b>Types:</b>

FIFO: No prioritization, first come, first served.

WFQ: Automatic weighted sharing.

CBWFQ: Custom classes and bandwidth assignment.

LLQ: Adds strict priority (voice/video must go first).

Example:
```
policy-map QOS_POLICY
 class VOICE
  priority 1000
 class VIDEO
  bandwidth 500
 class class-default
  fair-queue
```

### NBAR (Network-Based Application Recognition) 

NBAR is used to classify applications based on deep packet
inspection.

Recognizes traffic based on Layer 7 (Application Layer).

Useful for things like HTTP, Skype, YouTube detection.

NBAR is mandatory if you want to classify encrypted or dynamic ports.

<b>Example:</b>
```
class-map match-any WEB_TRAFFIC
 match protocol http
 match protocol https
```
### IPP (IP Precedence) Older
marking system (3 bits inside IP header).

0–7 range:

0: Best Effort

5: Voice

Largely replaced by DSCP.

### APP (Application Recognition) 

Not an official term like "IPP", but often used loosely in Cisco docs.

Means recognizing traffic based on the application, typically using NBAR.

DSCP (Differentiated Services Code Point) Modern marking system (6 bits inside IP header).

<b>Examples:</b>

EF (Expedited Forwarding) = DSCP 46 = Voice

AF41 (Assured Forwarding) = DSCP 34 = High-priority video

CS0 = Best Effort

DSCP defines Per-Hop Behaviors (PHBs) like priority or bandwidth guarantees.

DSCP Short List:

EF = 46 (Voice, must be delivered fast)

AF41 = 34 (Video conferencing)

AF21 = 18 (Bulk data, less important)

CS0 = 0 (Default traffic)

### Shaping 

Shaping buffers (queues) excess traffic and sends it later.

Purpose: Control traffic rate gently to avoid packet loss.

Used on outbound traffic.

Creates delays but avoids drops.

<b>Example:</b>
```
policy-map SHAPE_POLICY
 class class-default
  shape average 1000000
  ```
 ### Congestion Avoidance
 
Congestion Avoidance techniques try to predict and prevent congestion before it happens.

<b>Example:</b> WRED (Weighted Random Early Detection).

WRED randomly drops packets before the queue is full, mostly for lower-priority traffic.

High-priority traffic (like voice) is usually exempted.

<b>Example of WRED activation:</b>
```
policy-map CONGESTION_AVOID
 class class-default
  random-detect
```
WRED drops low-priority packets first,
keeping important traffic flowing.

### Summary of Flow 


Classification: Identify the type of traffic.

Marking: Mark packets with DSCP/CoS values.

Queuing: Assign queues and priorities.

Shaping: Slow down sending rate without dropping.

Congestion Avoidance: Drop low-priority packets early using WRED.

Scheduling: Decide order of sending packets out.
