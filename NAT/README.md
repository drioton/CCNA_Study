# NAT Overview

## Why NAT exists

- IPv4 address space is limited (only ~4.3 billion addresses)
- NAT allows private (RFC1918) addresses to access public networks
- Prevents address conflicts in overlapping networks
- Enables network growth without requiring public IPs
- Simplifies internal IP management
- Adds a layer of abstraction between internal and external hosts

## RFC1918 Private IP ranges

- 10.0.0.0 – 10.255.255.255
- 172.16.0.0 – 172.31.255.255
- 192.168.0.0 – 192.168.255.255

## Advantages

- Conserves public IPv4 addresses
- Internal topology is hidden
- IP reuse across sites
- Enables multihomed Internet connectivity

## Disadvantages

- Breaks true end-to-end communication
- Makes troubleshooting harder (hidden IPs)
- Breaks some protocols (e.g. SIP, FTP active mode)
- Adds overhead to routers (especially with PAT)

# NAT Types

## Static NAT

- 1:1 mapping between internal and external IP
- Permanent translation (manual)
- Typically used for internal servers accessible from the internet

### Command

```
ip nat inside source static 192.168.1.10 203.0.113.10
```
## Dynamic NAT

- Internal IPs are mapped to public IPs from a pool
- Mappings are not fixed, but dynamically assigned
- Requires:
  - Access List to define internal IPs
  - NAT Pool to define available public IPs

### Configuration

```
ip nat pool MYPOOL 203.0.113.10 203.0.113.20 netmask 255.255.255.0
access-list 1 permit 192.168.1.0 0.0.0.255
ip nat inside source list 1 pool MYPOOL
```
## PAT (Port Address Translation)

- Many-to-one translation
- Also called NAT overload
- Uses ports to differentiate sessions
- Most commonly used NAT type
```
access-list 1 permit 192.168.1.0 0.0.0.255
ip nat inside source list 1 interface Gig0/1 overload
```

# PAT Translation Table Example

| Inside Local           | Inside Global         | Outside Global       | Protocol | Port |
|------------------------|-----------------------|----------------------|----------|------|
| 192.168.1.10:1025      | 203.0.113.1:30001     | 8.8.8.8:53           | UDP      | DNS  |


Inside Local: IP address of internal host (before NAT)

Inside Global: IP address assigned by NAT (seen by outside world)

Outside Global: IP address of destination (e.g., Internet server)

Each new session uses a new port number on the Inside Global address



## Translation Table for PAT (Port Address Translation)

| Inside Local (IP:Port) | Inside Global (IP:Port) | Outside Global (IP:Port) | Protocol | Port |
|------------------------|--------------------------|--------------------------|----------|------|
| 10.0.0.2:1025          | 203.0.113.1:30001        | 8.8.8.8:53               | UDP      | DNS  |
| 10.0.0.3:1045          | 203.0.113.1:30002        | 142.250.74.206:80        | TCP      | HTTP |
| 10.0.0.2:1050          | 203.0.113.1:30003        | 1.1.1.1:443              | TCP      | HTTPS|
| 10.0.0.4:1080          | 203.0.113.1:30004        | 8.8.8.8:53               | UDP      | DNS  |
| 10.0.0.5:1100          | 203.0.113.1:30005        | 93.184.216.34:443        | TCP      | HTTPS|

<b>Explanation:</b>
<b>Inside Local:</b> Private IP address of internal host (with the port number)

<b>Inside Global:</b> Translated public IP address and port number (as seen by the outside world)

<b>Outside Global:</b> Destination IP address and port number (the real world address)

<b>Protocol:</b> The protocol used (e.g., UDP or TCP)

<b>Port:</b> The specific port number used in the translation

# NAT Terminology

| Term             | Description                                                                       |
|------------------|-----------------------------------------------------------------------------------|
| Inside Local     | IP address assigned to a host on the inside network (before translation)         |
| Inside Global    | Translated public IP representing internal host on the internet                  |
| Outside Local    | IP address of an external host as it appears to the internal network (rarely used) |
| Outside Global   | Real IP address of the external host (e.g. web server, DNS, etc.)                |

## Example

- Internal Host: 192.168.1.10
- NAT Router translates to: 203.0.113.5
- Host accesses: 8.8.8.8 (Google DNS)

| Role             | IP            |
|------------------|---------------|
| Inside Local     | 192.168.1.10  |
| Inside Global    | 203.0.113.5   |
| Outside Local    | 8.8.8.8       |
| Outside Global   | 8.8.8.8       |

*In most cases, Outside Local = Outside Global (no NAT on outside)*

# NAT vs IPv6

## Why NAT is not needed in IPv6

- IPv6 provides 2^128 addresses – enough for every device to have a public IP
- NAT violates the end-to-end principle of internet
- IPv6 promotes direct device communication

## Addressing in IPv6

- Devices typically get global unicast addresses
- Privacy features:
  - Temporary addresses (RFC4941)
  - Prefix delegation with DHCPv6 or SLAAC
- Stateful firewalls still used for security

## NAT66

- Rarely used in enterprise networks
- Used only in certain translation scenarios (IPv6 to IPv6 NAT)
- **Not recommended by IETF**

## Summary

| Feature           | IPv4 NAT        | IPv6                   |
|------------------|------------------|------------------------|
| Address saving    | Yes              | Not needed             |
| Breaks IPsec      | Yes              | No                     |
| End-to-end        | Broken           | Preserved              |
| Preferred method  | NAT + PAT        | Global + Firewall      |


# NAT with ACL 

## Overview

**Network Address Translation (NAT)** is a method used to translate private internal IP addresses to public IP addresses for communication over the Internet. **Access Control Lists (ACLs)** are used to control traffic flow and specify which traffic is eligible for NAT. By combining these two technologies, we can control which internal devices are allowed to access the internet and map them to specific public IP addresses.

---

## Why Use NAT with ACL?

- **Security**: ACLs restrict the internal network to only authorized devices, reducing the risk of unauthorized access.
- **Traffic Management**: ACLs help in managing which traffic can be translated using NAT, providing finer control over resource usage.
- **IP Conservation**: By allowing only certain internal IPs to access the internet via NAT, we save on public IP address usage.

---

## How NAT with ACL Works

1. **ACL Definition**: An ACL is created to define which internal IP addresses should be translated by NAT. Only the IP addresses that match the ACL are eligible for NAT.
   
2. **NAT Configuration**: After defining the ACL, a NAT rule is configured to use that ACL for translation. The translation process will then be performed only for the addresses matching the ACL.

3. **Translation Process**: When a device sends traffic to the public network (e.g., the Internet), the router uses NAT to replace the internal private IP with a public IP, and the ACL determines whether or not that device's traffic will be translated.

### Example Configuration

- Inside interface (LAN)
```
interface GigabitEthernet0/0
 ip address 192.168.1.1 255.255.255.0
 ip nat inside
```

- Outside interface (WAN)
```
interface GigabitEthernet0/1
 ip address 203.0.113.1 255.255.255.0
 ip nat outside
```
- Define ACL to match internal IPs
```
access-list 10 permit 192.168.1.0 0.0.0.255
```
- Enable NAT using the ACL
```
ip nat inside source list 10 interface GigabitEthernet0/1 overload
```
### Advantages of NAT with ACL
- Improved Security: Only devices permitted by the ACL can use NAT to access the Internet. This adds an extra layer of security by limiting which devices can initiate connections to external networks.

- Controlled Access: You can control which internal devices are allowed to use a public IP and how they interact with the outside world.

- Efficient IP Usage: By limiting NAT to specific IP addresses, you can save on the usage of public IP addresses and ensure they are only used by necessary devices.

- Simplified Administration: ACLs allow network administrators to create specific rules for NAT translation, simplifying network management and troubleshooting.

### Disadvantages of NAT with ACL
- Complex Configuration: Setting up NAT with ACLs requires careful planning and configuration. Incorrectly defined ACLs or NAT rules can lead to network outages or unauthorized access.

- Increased Overhead: Maintaining ACLs and NAT rules adds additional processing load on the router, which can impact network performance, especially in large-scale environments.

- Limited Flexibility: While ACLs provide control over which internal IPs can use NAT, this can limit the flexibility of NAT, especially in dynamic environments where devices join and leave the network frequently.

- Troubleshooting Complexity: Identifying issues with NAT translations can become complicated if ACLs are not configured properly, especially if multiple ACLs are in place for different purposes.

### When to Use NAT with ACL
When you need to allow only certain internal devices to access external resources (e.g., Internet, specific external servers).

When you want to conserve public IP addresses by translating only specific internal IPs.

In environments with security concerns, where you need to restrict external access to specific devices.

