# Syslog Server – Complete Overview

## What is Syslog?
Syslog is a standardized protocol for sending log messages across a network.\
Devices like routers, switches, firewalls, and servers can all send system events to a centralized Syslog Server.\
Standardized: RFC 3164 → RFC 5424

<b>Protocol:</b> Mostly UDP 514 (can also use TCP or TLS for secure transport)

<b>Message format:</b> Includes severity level, facility, timestamp, and message

### How Does Syslog Work?
A device generates a log message.

It’s tagged with a priority (facility + severity).

The message is sent to the configured Syslog server.

The server logs, displays, or forwards the message based on rules.

### Syslog Severity Levels (RFC 5424)

| Level      | Code | Meaning                  |
|------------|------|--------------------------|
| Emergency  | 0    | System is unusable       |
| Alert      | 1    | Immediate action needed  |
| Critical   | 2    | Critical condition       |
| Error      | 3    | Runtime error            |
| Warning    | 4    | Non-critical issue       |
| Notice     | 5    | Significant, normal event|
| Info       | 6    | Informational message    |
| Debug      | 7    | Debugging message        |

### Why Use Syslog?
Centralizes all logs into one location

Easier troubleshooting and root cause analysis

Helps with compliance (audit logs, forensic data)

Integrates with SIEM for real-time threat detection

Compatible with almost every modern network device

### It’s recommended to:

Use a separate virtual or physical server for logging

Store logs in secure, append-only storage

Limit access with firewalls and user permissions

Regularly back up logs

### Glossary of Important Terms
| Term                 | Meaning                                                                 |
|----------------------|-------------------------------------------------------------------------|
| Syslog               | Protocol for logging events over the network                            |
| Trap                 | Event notification sent when severity level matches or exceeds threshold|
| Informational        | Log level 6 – general system events (not errors)                        |
| Logging Buffer       | Internal memory on Cisco device for storing logs temporarily            |
| Logging Host         | IP address of Syslog Server (where logs are sent)                       |
| Logging Trap Level   | Minimum severity level to be sent to Syslog Server                      |
| Source Interface     | Interface used to send logs (important for IP-based filters)            |
| Timestamp            | Adds time info to log entries                                           |
| Facility             | Numeric category (e.g. auth, local0) used in message priority           |
| Priority             | Combination of Facility and Severity in one value (0–191)               |
| `service timestamps` | Cisco command that adds time info to logs                               |

### Logging Buffer vs Syslog Server

|Feature               | Logging Buffer             | Syslog Server|
|----------------------|---------------------------|---------------------------------|
|Location              | On the Cisco device       | External machine                |
|Persistent            | ❌ (cleared after reboot) | ✅ (can store logs long-term)   |
|Storage capacity      | Very limited (few KB/MB)  | Large disks                     |
|Use case              | Quick debug, local logs   | Centralized logging, auditing   |

### Severity vs Priority vs Facility
Let’s break it down:

Severity: Log level (0–7), tells how bad the message is

Facility: Group/type of process (e.g., kernel, auth, local7)

Priority: Single number = (facility × 8) + severity

Example: Facility = 4 (auth), Severity = 3 (error) → Priority = 35

Cisco doesn’t use these numbers directly in commands but Syslog servers do.

### Syslog Facilities Table

| Facility Number | Facility Name    | Description                                 |
|------------------|------------------|---------------------------------------------|
| 0                | kernel           | Kernel messages                             |
| 1                | user             | User-level messages                         |
| 2                | mail             | Mail system                                 |
| 3                | daemon           | System daemons                              |
| 4                | auth             | Security/authorization messages             |
| 5                | syslog           | Internal syslog messages                    |
| 6                | lpr              | Line printer subsystem                      |
| 7                | news             | Network news subsystem                      |
| 8                | uucp             | Unix-to-Unix Copy messages                  |
| 9                | clock daemon     | System clock (cron/at)                      |
| 10               | authpriv         | Security/authorization (private)            |
| 11               | ftp              | FTP daemon messages                         |
| 12–15            | -                | Reserved                                    |
| 16–23            | local0–local7    | Custom use (common for Cisco logging)       |

Cisco typically uses local0 to local7 when sending logs to an external Syslog server.
On the server side (e.g. in /etc/rsyslog.conf), you can filter logs like this:
```
local7.*   /var/log/cisco.log
```


### Syslog + SIEM
Syslog feeds raw logs into a SIEM (like Splunk, ELK, Graylog)

SIEM ingests the data via Syslog

Parses and normalizes events

Applies rules, alerts, and correlation

Detects threats like brute-force login attempts or lateral movement

### Filtering Logs Examples
You don’t want to log everything. 
+ Cisco - Logs only levels 0–4 (Emergency to Warning), skipping less relevant Info or Debug.
```
(config)# logging trap warnings
```
+ rsyslog 
```
# Only capture auth errors
authpriv.*    /var/log/auth.log
```
+ Advanced
```
:msg, contains, "Failed password"    /var/log/ssh_failures.log
```

### Log Rotation Example 
- Linux – logrotate
Avoid filling up disk space:
/etc/logrotate.d/syslog
```
/var/log/syslog {
    rotate 7
    daily
    missingok
    compress
    delaycompress
    notifempty
    create 640 syslog adm
}
```
<b>This:</b>

+ Keeps 7 days of logs

+ Compresses older logs

+ Skips if log file is empty

###  Cisco Syslog Configuration
```
# Set Syslog server IP
(config)# logging 192.168.1.100

# Filter by severity
(config)# logging trap warnings

# Set source interface
(config)# logging source-interface GigabitEthernet0/1

# Add timestamps to logs
(config)# service timestamps log datetime msec
```
### Linux Client Example (rsyslog)
```
# In /etc/rsyslog.conf or /etc/rsyslog.d/50-default.conf

*.*   @192.168.1.100:514

# Restart service
sudo systemctl restart rsyslog
```
# SNMP Explained

## 1. Community Strings
| Community String | Meaning          | Permissions | Default Examples |
|------------------|------------------|-------------|------------------|
| public           | Read-only (RO)   | ✅          | RO: read status  |
| private          | Read-write (RW)  | ⚠️          | RW: change config|

- Used like passwords between SNMP manager and agent

- Case-sensitive 

- Can limit access 
 RO: Read-only (for monitoring)

 RW: Read-write (can change config – dangerous)
```
snmp-server community public RO
snmp-server community private RW
```
- For security: use a custom string, not "public"/"private".

## 2. SNMP Traps vs Polling

| Feature      | SNMP Traps                          | SNMP Polling                        |
|--------------|--------------------------------------|-------------------------------------|
| Direction    | Agent → Manager                      | Manager → Agent                     |
| Trigger      | Automatic (event-based)              | Manual or scheduled                 |
| Bandwidth    | Low (only when needed)               | Higher (regular polling)            |
| Delay        | Immediate                            | Depends on interval                 |
| Use case     | Alerts (interface down)              | Monitoring (CPU, memory, interface) |

Traps are automatic alerts sent from agent to manager.

Polling is when the manager actively queries the agent.

## 3. SNMP Manager vs SNMP Agent

| Role           | Description                        | Example                            |
|----------------|------------------------------------|------------------------------------|
| SNMP Agent     | The Cisco device (router/switch)   | Configured with `snmp-server ...` |
| SNMP Manager   | Monitoring software                | PRTG, SolarWinds, LibreNMS         |

Agent = provides data

Manager = collects and visualizes data

## 4. Basic SNMP Config on Cisco
```
enable
configure terminal

snmp-server community public RO
snmp-server community private RW
snmp-server host 192.168.1.100 version 2c public
snmp-server enable traps
snmp-server enable traps snmp linkdown linkup
```
- community → defines access levels

- host → sets where SNMP traps are sent

- enable traps → turns on trap sending

- linkdown/linkup → enables specific alerts

## 5. Common Traps: linkdown / linkup
 
 | Trap Type  | What it means              | Trigger Example                    |
|------------|----------------------------|------------------------------------|
| linkdown   | Interface went DOWN        | Cable unplugged / port shutdown    |
| linkup     | Interface came UP          | Cable plugged back / port restored |

Example : The SNMP agent will send linkdown then linkup traps to the SNMP manager.
```
interface FastEthernet0/1
shutdown
no shutdown
```
## 6. SNMP Traps vs Syslog Traps

| Feature             | Syslog Trap                        | SNMP Trap                          |
|---------------------|-------------------------------------|-------------------------------------|
| Protocol            | Syslog (UDP/514)                    | SNMP (UDP/162)                      |
| Format              | Text-based log message              | Binary encoded message              |
| Trigger             | Severity threshold (e.g. level 4)   | Specific event/OID occurrence       |
| Use case            | General logging                     | Monitoring, automated alerts        |
| Real-time?          | Depends (buffered/logged)           | Yes, pushed immediately             |
| Reliability         | Best-effort (UDP, no ACK)           | Best-effort (UDP, but trap retries) |
| Config on Cisco     | `logging trap level`                | `snmp-server enable traps`          |
| Receiver            | Syslog server (e.g. Graylog)        | SNMP Manager (e.g. PRTG, SolarWinds)|
| Filtering           | By severity level                   | By OID/event type                   |


<b>Syslog traps = </b> basic severity-based event logs. You set a threshold, and everything equal or worse is sent.

<b>SNMP traps =</b> detailed, structured alerts triggered by specific events (e.g. interface down, CPU threshold hit).

<b>SNMP traps</b> are used more for automation and monitoring, while Syslog is for auditing and diagnostics.

##  7. Summary
Understand the difference between SNMP traps (push alerts) and polling (queries)

Know basic Cisco SNMP configuration

Recognize important traps like linkdown and linkup

Know what community strings are and how they affect SNMP access