# SNMP 
## 1. What is SNMP
SNMP = Simple Network Management Protocol.

Used to monitor and manage network devices (routers, switches, servers).

### Chapter 2: SNMP Ports and Communication

### SNMP Ports

| **Port** | **Purpose**                                      |
|----------|--------------------------------------------------|
| 161      | Used for **regular SNMP communication** (Get, Set, etc.) between the **Manager** and the **Agent**. |
| 162      | Used for **Traps** – unsolicited notifications sent by the **Agent** to the **Manager** when an event occurs. |

---

### SNMP Communication Flow

| **Role**   | **Description**                                                                                           |
|------------|-----------------------------------------------------------------------------------------------------------|
| **Manager** | A system responsible for managing network devices, sending requests to **Agents**, and receiving their responses. |
| **Agent**  | A software component on a network device that listens for requests from the **Manager** and responds with the requested data. |

The **Manager** sends a **Get** or **Set** request to the **Agent**, and the **Agent** responds with the requested information or performs the required configuration change. **Traps** are sent by the **Agent** when significant events occur.

---

## Chapter 3: SNMP Security and Operation

### SNMP Security in SNMPv1 and SNMPv2c

| **Community String Type** | **Access Level**                                      | **Description**                                             |
|---------------------------|-------------------------------------------------------|-------------------------------------------------------------|
| **RO (Read-Only)**         | Read-only access                                     | The manager can **only read data** from the agent.           |
| **RW (Read-Write)**        | Read and write access                                | The manager can **read and modify data** on the agent.       |

In **SNMPv1** and **SNMPv2c**, community strings are transmitted in **plaintext** and are not secure. For stronger security, **SNMPv3** should be used.

---

### SNMPv3 Security Features

| **Feature**    | **Description**                                                   |
|----------------|-------------------------------------------------------------------|
| **Authentication** | Verifies the identity of the sender (either Manager or Agent) to ensure only authorized devices can communicate. |
| **Encryption** | Ensures that the data sent between the Manager and Agent is **encrypted** and cannot be intercepted or altered. |

**SNMPv3** provides enhanced security with both authentication and encryption compared to the basic community string security in **SNMPv1** and **SNMPv2c**.

---

### SNMP Operations

| **Operation** | **Description**                                                                                           |
|---------------|-----------------------------------------------------------------------------------------------------------|
| **Get**       | The Manager requests a **single value** from the Agent (e.g., status of an interface or CPU load).         |
| **Set**       | The Manager sends a command to the Agent to **modify a value** (e.g., change configuration of an interface). |
| **GetNext**   | The Manager requests the **next value** from the Agent (useful for retrieving a list of values sequentially). |
| **GetBulk**   | The Manager requests **large amounts of data** at once (more efficient than multiple GetNext operations).   |
| **Trap**      | The Agent sends an **unsolicited message** to the Manager when a significant event occurs (e.g., failure).   |
| **Inform**    | Similar to a **Trap**, but the Manager must acknowledge receipt of the message, ensuring **reliability**.    |

---

### 4. SNMP and MIB

| **Component** | **Description**                                                                                              |
|---------------|--------------------------------------------------------------------------------------------------------------|
| **MIB**       | The **Management Information Base** is a **database of objects** that can be monitored or managed via SNMP. Each object is identified by an **OID (Object Identifier)** and represents a specific parameter or statistic (e.g., interface stats, CPU usage, network traffic). |
| **OID**       | An **Object Identifier** is used to uniquely identify each object in the MIB. It is structured as a tree with a hierarchical numbering scheme. |


## 5. SNMP Security Concepts

Term	Description
Integrity	Ensures data is not tampered during transmission (SNMPv3).
Authentication	Confirms the identity of the sender (SNMPv3).
Encryption (Privacy)	Scrambles SNMP messages so they can't be read in transit (SNMPv3).
Summary: Only SNMPv3 provides authentication + encryption.

## 6. Important SNMP Commands
snmpget – Retrieve a specific object from the agent.

snmpwalk – Walk through a part of MIB tree, retrieve multiple objects.

snmpset – Modify a value on the agent.

snmptrap – Send a trap message to an SNMP manager.


## 7. Typical SNMP Configuration (Cisco CLI Overview)
For SNMPv2c:

```
snmp-server community public RO
snmp-server community private RW
snmp-server host 10.0.0.5 version 2c public
```
For SNMPv3 (example):
```
snmp-server group SNMPv3Group v3 priv
snmp-server user SNMPv3User SNMPv3Group v3 auth sha MyAuthPass123 priv aes 128 MyPrivPass123
snmp-server host 10.0.0.5 version 3 priv SNMPv3User
```

## 8. Exam Focus Points (Summary)
* SNMP is used for monitoring and managing devices.
* SNMPv1 and v2c are insecure, v3 is secure.
* Manager = NMS, Agent = Device (router, switch, etc.).
* MIB contains all manageable objects (organized hierarchically).
* Community Strings = weak passwords for v1/v2c.
* Trap = notification sent by device without being asked.
* SNMPv3 offers authentication and encryption.

### Short version to memorize:
SNMP lets a Manager monitor Agents using Get/Set commands based on MIB data, with security depending on the version (only SNMPv3 is secure).

## 9. # SNMP Questions and Answers

### Q1: What is the main purpose of SNMP?
The main purpose of SNMP is to **monitor and manage network devices** by collecting data and configuring devices (e.g., routers, switches, servers) remotely.

---

### Q2: Which ports does SNMP use for:
- regular communication (Get/Set)?
- traps?

- **Port 161** – for regular communication (Get/Set).
- **Port 162** – for **Traps**.

---

### Q3: Who initiates the communication – the Manager or the Agent?
**The Manager** initiates the communication and sends requests to the **Agent**.

---

### Q4: What does MIB stand for and what is inside?
**MIB (Management Information Base)** is a hierarchical database that defines the objects that can be monitored or managed via SNMP, such as device attributes (e.g., interface stats, CPU load).

---

### Q5: What is the difference between Trap and Inform?
- **Trap**: An unsolicited message sent by the agent to the manager, usually when a certain event occurs (e.g., an alert, failure). It does not require confirmation from the manager.
- **Inform**: Similar to a Trap, but requires an acknowledgment from the manager to confirm that the message was received.

---

### Q6: In SNMPv2c, what provides "security"?
In **SNMPv2c**, **community strings** provide basic security (i.e., they act like passwords). However, these strings are sent in plaintext and are not highly secure. For more advanced security, **SNMPv3** should be used.

---

### Q7: What are the two types of community strings?
- **RO (Read-Only)**: Allows the manager to **read** data from the device but **not modify** it.
- **RW (Read-Write)**: Allows the manager to **read and modify** data on the device.

---

### Q8: Which SNMP version supports encryption and authentication?
**SNMPv3** supports both **encryption** and **authentication**, providing better security compared to **SNMPv1** and **SNMPv2c**.

---

### Q9: When using SNMPv1 or SNMPv2c, are the community strings encrypted?
No, **community strings** in **SNMPv1** and **SNMPv2c** are not encrypted. They are sent as plaintext and can be intercepted.

---

### Q10: Name two examples of an NMS (Network Management System) software.
- **SolarWinds**
- **Cisco Prime**

---

### Q11: What SNMP operation retrieves a single value from an agent?
The **Get** operation retrieves a single value from an agent.

---

### Q12: Which SNMP operation is used to get large amounts of data at once?
**GetBulk** is used to efficiently retrieve large amounts of data from an agent.

---

### Q13: What does "integrity" mean in the context of SNMPv3?
**Integrity** in **SNMPv3** ensures that the data has not been **tampered with** during transmission between the manager and the agent. It verifies the authenticity of the data.

---
