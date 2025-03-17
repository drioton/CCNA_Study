# SSH

You can continue from the diagram you created in the configuration repository  
```
enable
configure terminal
ip domain-name example.com
crypto key generate rsa general-keys modulus 1024
username Noro secret black
line vty 0 15
login local
transport input ssh
exit
exit
copy running-config startup-config
```





### Test Telnet

```
C:\>telnet 192.168.1.33
Trying 192.168.1.33 ...Open

[Connection to 192.168.1.33 closed by foreign host]
```
### Test SSH
```
C:\>ssh -l Noro 192.168.1.33

Password: 
SW1>
```

Here's a short summary of the commands:

1. **enable**: Enters privileged EXEC mode.
2. **configure terminal**: Enters global configuration mode.
3. **ip domain-name example.com**: Sets the domain name for the device.
4. **crypto key generate rsa general-keys modulus 1024**: Generates RSA keys for SSH with a 1024-bit modulus.
5. **username Noro secret black**: Creates a user "Noro" with the password "black".
6. **line vty 0 15**: Configures virtual terminal lines (VTY) 0-15 for remote access.
7. **login local**: Specifies the use of local usernames and passwords for login.
8. **transport input ssh**: Enables SSH for remote access.
9. **exit**: Exits from line configuration mode.
10. **exit**: Exits from global configuration mode.
11. **copy running-config startup-config**: Saves the running configuration to the startup configuration.

These commands configure SSH access and user authentication for remote login on a Cisco device.





