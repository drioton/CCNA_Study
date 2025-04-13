# Access Control Lists (ACL) – CCNA Notes

# 1. ACL Number Ranges
Standard ACLs:
- 1–99
- 1300–1999

Extended ACLs:
- 100–199
- 2000–2699

These ranges tell the router what type of ACL it is. You can also use named ACLs for clarity.

# 2. remark
Used to add comments inside ACLs for better readability. Does not affect traffic.

Example:
access-list 110 remark Deny Telnet from 192.168.1.0/24

# 3. Standard vs Extended ACLs

Standard ACLs:
- Match only source IP
- Used for simple filtering
- Placed as close to the **destination** as possible

Extended ACLs:
- Match source, destination, protocol, ports
- Used for precise filtering
- Placed as close to the **source** as possible

# 4. resequence
Changes the sequence numbers of ACEs (Access Control Entries) for better organization.

Example:
ip access-list extended ACL_NAME
 resequence 10 10

This will renumber entries as 10, 20, 30, etc.

# 5. Command Syntax Overview

Apply ACL to interface (inbound/outbound):
```
interface GigabitEthernet0/1
 ip access-group 110 in
```

Apply ACL to VTY (for Telnet/SSH restrictions):
```
line vty 0 4
 access-class 50 in
```
View ACLs:
```
show access-lists
```
View interface ACL bindings:
```
show ip interface GigabitEthernet0/1
```
# 6. Examples – Protocol Filtering

## ICMP (Ping)
Block ICMP (all types):

```
access-list 110 deny icmp any any
access-list 110 permit ip any any
```

## OSPF
Block OSPF (protocol number 89):
```
access-list 110 deny 89 any any
access-list 110 permit ip any any`
```
## DNS (UDP port 53)
Block DNS requests:
```
access-list 110 deny udp any any eq 53
access-list 110 permit ip any any
```
## DHCP (UDP port 67/68)
Block DHCP:
```
access-list 110 deny udp any eq 67 any
access-list 110 deny udp any eq 68 any
access-list 110 permit ip any any
```
## Telnet (TCP port 23)
Block Telnet:
```
access-list 110 deny tcp any any eq 23
access-list 110 permit ip any any
```
## SSH (TCP port 22)
Block SSH:
```
access-list 110 deny tcp any any eq 22
access-list 110 permit ip any any
```

# 7. Port Operators in Cisco ACLs

### eq – Equal to
+ Matches TCP traffic to port 80 (HTTP)
```
access-list 110 permit tcp any any eq 80

```
### gt – Greater than
```
access-list 110 deny tcp any any gt 1023
```
+ Denies TCP traffic to ports greater than 1023

### lt – Less than
```
access-list 110 deny udp any any lt 1024
```
+ Denies UDP traffic to ports less than 1024

### neq – Not equal to
```
access-list 110 permit tcp any any neq 25
```
+ Permits TCP traffic to all ports except 25 (SMTP)

### range – Range of ports
```
access-list 110 deny tcp any any range 20 21
```
+ Denies TCP traffic to ports from 20 to 21 (FTP)

# 8. Additional Information on ACLs

### Implicit Deny
Every ACL in Cisco has an implicit **deny all** at the end. This means that if you don't specifically allow a type of traffic, it will be denied by default. Always ensure you have a `permit` at the end if you want to allow some traffic.

### Wildcard Masks
A wildcard mask is used with ACLs to define the range of addresses more flexibly. Unlike a subnet mask, which defines which bits of an address are part of the network, a wildcard mask defines which bits should be matched.

**Example:**
```
access-list 10 permit 192.168.1.0 0.0.0.255
```
This means "allow any address from 192.168.1.0 to 192.168.1.255."

0.0.0.255 means "any address where the first 24 bits are 192.168.1, and the last 8 bits can be anything (0-255)."
###  Named ACLs
Instead of using number-based ACLs (like access-list 10), you can name your ACLs for better clarity.Example:
```
ip access-list standard MyACL
permit 192.168.1.0 0.0.0.255
deny any
```
Named ACLs are easier to manage and modify, especially for complex configurations.

### ACL Matching Logic
Cisco ACLs process each entry from top to bottom. If a match is found, the corresponding action (permit/deny) is applied, and further entries are not checked.
Tip: When writing ACLs, always put the most specific matches at the top and general ones (like permit ip any any) at the bottom.

### Extended ACLs with Logging
You can log matches on an ACL by adding the log keyword.Example:
```
access-list 110 deny tcp any any eq 23 log
```
This logs any denied Telnet traffic (TCP port 23).

### Reflexive ACLs
These are dynamic ACLs that allow return traffic for connections initiated from inside the network.Example:

access-list 100 permit tcp host 192.168.1.10 any eq 80
ip access-group 100 in
This allows outbound traffic and establishes a reflexive entry for the return traffic, which is automatically allowed.

### Time-Based ACLs
Cisco supports time-based ACLs where you can permit or deny traffic based on the time of day.Example:

time-range work-hours
periodic weekdays 09:00 to 17:00
access-list 110 permit ip any any time-range work-hours
This ACL permits IP traffic during business hours (9:00 AM to 5:00 PM on weekdays). 

# 9. Notes & Best Practices
- ACLs are processed top-down; first match wins.
- There's an implicit "deny all" at the end.
```
access-list 110 deny tcp any any eq 23      # Block Telnet
access-list 110 deny tcp any any eq 22      # Block SSH
access-list 110 permit ip any any           # Allow everything else
```

- Always include a final "permit" if you want to allow other traffic.
- Use "remark" to label what each entry is doing.
- Extended ACLs = more control; place near source.
- Standard ACLs = simpler; place near destination.
