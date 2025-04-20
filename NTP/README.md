# NTP (Network Time Protocol) 

## What is NTP

NTP is used to synchronize the time across all network devices. Accurate time is essential for logging, security, and coordination between devices.

### Key use cases:
- Syslog timestamps
- Debug and event log accuracy
- ACL log entries
- Certificate validity
- SIEM correlation
- Routing protocols and network forensics

## NTP Stratum Levels

- Stratum 0: Reference clock (GPS, atomic)
- Stratum 1: Directly connected server to Stratum 0
- Stratum 2+: Devices synchronized with a higher-stratum server

## NTP Configuration

### Set timezone

```
clock timezone CET 1
Configure a device as NTP client
ntp server 192.168.1.1
```
Note: This IP is the next-hop towards an NTP source. It does not need to be the end device with Stratum 1.

Configure a router as an NTP server
```
ntp master 3
```
Optional: the number defines the stratum level.

Verification Commands
```
show clock detail
```
Shows local time, time source, and sync status.
```
show ntp associations
```
Displays peers, their stratum, and reachability status.
```
show ntp status
```
Shows whether the device is synchronized, with what server, and at what stratum level.

 Authentication (MD5)
```
ntp authenticate
ntp authentication-key 1 md5 SecretKey123
ntp trusted-key 1
ntp server 192.168.1.1 key 1
```
### What Goes Wrong Without Proper Time Sync
Logs become inconsistent and unusable for analysis

ACL log entries may not match events in time

Certificate-based authentication may fail due to invalid timestamps

SIEM and log analysis tools can’t correlate events

In routing protocols, troubleshooting adjacency issues becomes unclear

### Which IP Do You Configure?
If you have this path:

RouterA → RouterB → RouterC → RouterD (NTP server)

You configure:
```
ntp server [IP address of RouterB]
```
You configure the next reachable router in the direction of the NTP server.

Each router can act as a client/server depending on its config.

You don’t need to configure the end NTP server IP if the path is chained correctly.

### Why NTP Matters
If devices are out of sync, this can happen:

Logs are unusable for forensics

Time-based ACLs don't work as expected

Certificate authentication may fail

SIEMs and monitoring tools can't correlate logs

Routing protocol events become difficult to trace

### Summary
Use ntp server to configure clients

Use ntp master to simulate an NTP server

Time sync is critical for logging, security, and network consistency