

**PC0** 192.168.1.2

**PC1** 192.168.1.3

**PC2** 192.168.2.2

**PC3** 192.168.2.3

**PC4** 192.168.3.2

**PC5** 192.168.3.3



**Router 0(Left)**
```
enable
configure terminal
hostname Left
interface GigabitEthernet0/1
ip address 192.168.1.1 255.255.255.0
no shutdown
exit
interface GigabitEthernet0/0
ip address 1.0.0.1 255.255.255.252
no shutdown
exit
router rip
version 2
no auto-summary
network 192.168.1.0
network 1.0.0.0
passive-interface GigabitEthernet0/1
exit
exit
copy running-config startup-config
```



**Router 1(Middle)** 
```
enable
configure terminal
hostname Middle
interface GigabitEthernet0/1
ip address 192.168.2.1 255.255.255.0
no shutdown
exit
interface GigabitEthernet0/0
ip address 1.0.0.2 255.255.255.252
no shutdown
exit
router rip
version 2
no auto-summary
network 192.168.2.0
network 1.0.0.0
passive-interface GigabitEthernet0/1
exit
```

**PC0** 
```
192.168.1.2
192.168.1.1
255.255.255.0
```
**PC1**
```
192.168.1.3
192.168.1.1
255.255.255.0
```
**PC3** 
```
192.168.2.2
192.168.2.1
255.255.255.0
```
**PC4**
```
192.168.2.3
192.168.2.1
255.255.255.0
```


**PC0**
```
C:\>ping 192.168.2.2

Pinging 192.168.2.2 with 32 bytes of data:

Reply from 192.168.2.2: bytes=32 time<1ms TTL=126
Reply from 192.168.2.2: bytes=32 time<1ms TTL=126
Reply from 192.168.2.2: bytes=32 time<1ms TTL=126
Reply from 192.168.2.2: bytes=32 time<1ms TTL=126

Ping statistics for 192.168.2.2:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
Minimum = 0ms, Maximum = 0ms, Average = 0ms
```

**PC3** 
```
C:\>tracert 192.168.1.3

Tracing route to 192.168.1.3 over a maximum of 30 hops: 

  1   2 ms      0 ms      0 ms      192.168.2.1
  2   0 ms      0 ms      0 ms      1.0.0.1
  3   *         0 ms      0 ms      192.168.1.3

Trace complete.
```

**Adding Router**
**Adding PC4 and PC5**    

**Router Right**
```
enable
configure terminal
hostname Right
interface GigabitEthernet0/2
no shutdown
ip address 2.0.0.2 255.255.255.252
exit
interface GigabitEthernet0/1
ip address 192.168.3.1 255.255.255.0
no shutdown
exit
router rip
version 2
no auto-summary
network 2.0.0.0
network 192.168.3.0
passive-interface GigabitEthernet0/1
exit
exit
copy running-config startup-config
```
**Router Middle**
```
enable
configure terminal
int g0/2
ip address 2.0.0.1 255.255.255.252
no shutdown
exit
router rip
network 2.0.0.0
exit
exit
copy running-config startup-config
```
**PC4**
```
192.168.3.2
192.168.3.1
255.255.255.0
```

**PC5**
```
192.168.3.3
192.168.3.1
255.255.255.0
```

**Test**
```
C:\>ping 192.168.1.2

Pinging 192.168.1.2 with 32 bytes of data:

Reply from 192.168.1.2: bytes=32 time<1ms TTL=125
Reply from 192.168.1.2: bytes=32 time<1ms TTL=125
Reply from 192.168.1.2: bytes=32 time<1ms TTL=125
Reply from 192.168.1.2: bytes=32 time<1ms TTL=125

Ping statistics for 192.168.1.2:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
```

**PC4**
```
C:\>tracert 192.168.2.3

Tracing route to 192.168.2.3 over a maximum of 30 hops: 

  1   0 ms      0 ms      18 ms     192.168.3.1
  2   0 ms      0 ms      0 ms      2.0.0.1
  3   0 ms      0 ms      0 ms      192.168.2.3

Trace complete.

C:\>tracert 192.168.1.2

Tracing route to 192.168.1.2 over a maximum of 30 hops: 

  1   0 ms      0 ms      0 ms      192.168.3.1
  2   0 ms      0 ms      0 ms      2.0.0.1
  3   0 ms      0 ms      0 ms      1.0.0.1
  4   0 ms      0 ms      0 ms      192.168.1.2

Trace complete.
```
**Router Left**
```
show ip rip database 
1.0.0.0/30    auto-summary
1.0.0.0/30    directly connected, GigabitEthernet0/0
2.0.0.0/30    auto-summary
2.0.0.0/30
    [1] via 1.0.0.2, 00:00:09, GigabitEthernet0/0
192.168.1.0/24    auto-summary
192.168.1.0/24    directly connected, GigabitEthernet0/1
192.168.2.0/24    auto-summary
192.168.2.0/24
    [1] via 1.0.0.2, 00:00:09, GigabitEthernet0/0
192.168.3.0/24    auto-summary
192.168.3.0/24
    [2] via 1.0.0.2, 00:00:09, GigabitEthernet0/0
```
**Router Right**
```
show ip protocols 
Routing Protocol is "rip"
Sending updates every 30 seconds, next due in 9 seconds
Invalid after 180 seconds, hold down 180, flushed after 240
Outgoing update filter list for all interfaces is not set
Incoming update filter list for all interfaces is not set
Redistributing: rip
Default version control: send version 2, receive 2
  Interface             Send  Recv  Triggered RIP  Key-chain
  GigabitEthernet0/2    22
Automatic network summarization is not in effect
Maximum path: 4
Routing for Networks:
	2.0.0.0
	192.168.3.0
Passive Interface(s):
	GigabitEthernet0/1
Routing Information Sources:
	Gateway         Distance      Last Update
	2.0.0.1              120      00:00:04
Distance: (default is 120)
```

**Router Middle**
```
show ip rip database 
1.0.0.0/30    auto-summary
1.0.0.0/30    directly connected, GigabitEthernet0/0
2.0.0.0/30    auto-summary
2.0.0.0/30    directly connected, GigabitEthernet0/2
192.168.1.0/24    auto-summary
192.168.1.0/24
    [1] via 1.0.0.1, 00:00:26, GigabitEthernet0/0
192.168.2.0/24    auto-summary
192.168.2.0/24    directly connected, GigabitEthernet0/1
192.168.3.0/24    auto-summary
192.168.3.0/24
    [1] via 2.0.0.2, 00:00:05, GigabitEthernet0/2
```

