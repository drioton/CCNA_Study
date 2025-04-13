# 1. AAA (Authentication, Authorization, and Accounting)
AAA is a framework used for managing network access.\
It consists of three main components that handle different aspects of user interaction with network devices:

### Authentication (A)
Verifying the identity of the user or device trying to access the network.\
This is typically done using usernames, passwords, and other methods (like certificates or multi-factor authentication).

### Authorization (A)
After authentication, authorization determines what actions or resources the authenticated user is allowed to access.\
For instance, a user may be authenticated to log in but may not have permission to configure network settings.

### Accounting (A)
Tracking what the user does on the network. This includes logging activities like login times, commands entered, resources accessed, and data used.

### How AAA Works in Practice
### User Login Request
A user attempts to access a network device (e.g., a router or switch).

+ Authentication Process
The device asks the user for their username and password. These credentials are sent to an AAA server (e.g., RADIUS or TACACS+) for verification.

+ Authorization Check
If authentication is successful, the AAA server checks the user's privileges and sends back the appropriate permissions.

+ Accounting Data Collection
Once access is granted, the AAA system begins logging the user’s activities (e.g., commands, login duration, resource usage).

+ Session Termination
When the user logs out or their session is terminated, the system ends accounting and updates the logs.

### Advantages of AAA
+ Centralized User Management:
AAA protocols (like RADIUS and TACACS+) allow centralized management of users and policies, making it easier to manage a large number of users.

+ Security and Control:
By separating authentication, authorization, and accounting, AAA systems provide detailed control over network access and user actions.

+ Auditing and Monitoring:
Accounting allows for tracking user activities, which is useful for auditing, compliance, and troubleshooting.

###  Use Cases of AAA
+ <b>VPN Authentication:</b> 
When users connect to a VPN, their identity can be verified via AAA, and their access rights can be assigned accordingly.

+ <b>Wi-Fi Access Control:</b>
In enterprise environments, users authenticate via a captive portal or Wi-Fi authentication system, with their actions tracked for compliance.

+ <b>Router/Switch Access Control:</b>
Network administrators can use AAA to control who can access network devices like routers or switches and what they can do (e.g., admin commands or limited read-only access).

### Configuration Example:
<b>Cisco Router AAA Configuration:</b> 
```
R1(config)# aaa new-model
R1(config)# aaa authentication login default local
R1(config)# aaa authorization exec default local
R1(config)# aaa accounting exec default start-stop group radius
```
In this example:

+ <b>aaa new-model:</b> Enables AAA on the router.

+ <b>aaa authentication login default local:</b> Uses the local database to authenticate login requests.

+ <b>aaa authorization exec default local:</b> Uses the local database to authorize user sessions.

+ <b>aaa accounting exec default start-stop group radius:</b> Uses RADIUS to track user activities.




# RADIUS (Remote Authentication Dial-In User Service)
<b>RADIUS</b> is a centralized authentication, authorization, and accounting protocol.\
It is used primarily for network access (e.g., connecting to a router, switch, or wireless network).

### How RADIUS Works
+ Authentication:
When a user attempts to connect to a network device (e.g., router), the network device sends a request to the RADIUS server with the user’s credentials.\
If the credentials are valid, the user is authenticated.

+ Authorization:
After authentication, the RADIUS server checks what resources the user can access based on pre-configured policies.

+ Accounting:
It can track the time a user spent on the network, the IP address assigned to them, and other actions they perform.

### 2. RADIUS Advantages:
Speed: RADIUS is fast because it sends requests in a single UDP packet (UDP 1812/1813 for authentication/accounting).

Scalability: Supports large-scale networks and is often used in ISP and enterprise environments.

Single point of management: User credentials and policies are managed centrally on the RADIUS server.

### RADIUS Disadvantages:
Less secure: Uses weaker encryption for authentication (only encrypts the password, not the whole packet).

Limited flexibility: Doesn't support granular command authorization or specific commands.

### Where RADIUS Works:
Wi-Fi networks, VPNs, and ISPs.

Enterprise network devices (routers, switches, firewalls, etc.) for user authentication and accounting.

# 3. TACACS+ (Terminal Access Controller Access-Control System Plus)
<b>TACACS+</b> is another AAA protocol, but it differs significantly from RADIUS in its implementation and functionality. It was developed by Cisco and is commonly used in Cisco environments.

### How TACACS+ Works
### Authentication:
Like RADIUS, TACACS+ authenticates the user, but TACACS+ sends the entire user request to the authentication server, which provides a more secure authentication process.

### Authorization:
After authentication, TACACS+ provides detailed command-level authorization. It can allow or deny specific commands on a device based on the user profile.

### Accounting:
Tracks user sessions, logging the commands they executed, and the resources they accessed, similar to RADIUS.

### TACACS+ Advantages:
+ Stronger security:
TACACS+ encrypts the entire packet, including the username, password, and the authentication request, offering better protection over RADIUS.

+ Granular control:
Provides more granular control over what specific commands users can run, which is ideal for administrators.

+ Separate services:
Separates authentication, authorization, and accounting, which gives more flexibility and control.

###  TACACS+ Disadvantages:
+ Slower than RADIUS:
Because TACACS+ uses TCP (port 49), it's generally slower than RADIUS, which uses UDP.

+ More complex setup:
It’s more complex to configure and maintain TACACS+ servers, especially when compared to RADIUS.

+ Where TACACS+ Works:
Cisco environments and devices that require command-level access control.

Enterprise networks where detailed logging and fine-grained command authorization are essential.

### Why is TACACS+ Proprietary?
+ Cisco’s Customization:
TACACS+ was developed by Cisco to address specific needs for secure network device access. It offers greater flexibility, more granular command-level control, and full encryption of the entire packet, which makes it especially useful for network administration.

+ Not Open Standard:
Unlike RADIUS, which is an open standard protocol, TACACS+ is not standardized and is unique to Cisco. This means that only Cisco devices natively support it, and it is primarily used in Cisco environments.

### Key Features of TACACS+ (Proprietary)
<b>Full Encryption:</b>
TACACS+ encrypts the entire packet, including the username, password, and command, offering a higher level of security compared to RADIUS, which only encrypts the password.

<b>Separate AAA Functions:</b>
TACACS+ separates authentication, authorization, and accounting into distinct processes, making it more flexible in assigning permissions and managing user roles.

<b>Command-Level Control:</b>
TACACS+ allows administrators to set granular command authorization, giving them the ability to control which commands a user can execute on network devices.

<b>RADIUS vs. TACACS+ (Proprietary)</b>
While RADIUS is an open standard used for various types of access control (e.g., VPN, wireless), TACACS+ is typically used in environments where detailed administrative control is needed, especially in Cisco network devices.

### Use of TACACS+ in Cisco Network Devices
<b>Network Administration:</b>
TACACS+ is primarily used for administrative access to Cisco network devices (routers, switches, firewalls, etc.) because it offers a higher level of security and command control.

<b>AAA Services for Cisco Devices:</b>
It is often used to provide centralized user authentication, define authorization policies, and track accounting logs for users managing Cisco devices.

### TACACS+ Configuration Example in Cisco Router:
```
R1(config)# tacacs-server host 192.168.1.100 key mytacacskey
R1(config)# aaa new-model
R1(config)# aaa authentication login default group tacacs+ local
R1(config)# aaa authorization exec default group tacacs+ local
R1(config)# aaa accounting exec default start-stop group tacacs+
```
# When to Use RADIUS or TACACS+
<b>RADIUS</b> is best for environments where speed and scalability are more critical, such as VPNs, Wi-Fi networks, or remote network access.

<b>TACACS+</b> should be used in Cisco-based environments where granular control over command-level access and higher security is required, such as for network administrators who need precise control over device configurations.

### Example Commands for RADIUS and TACACS+ Configuration on Cisco Routers
Configuring RADIUS:

```
R1(config)# radius-server host 192.168.1.100 key myradiuskey
R1(config)# aaa new-model
R1(config)# aaa authentication login default group radius local
R1(config)# aaa authorization exec default group radius local
R1(config)# aaa accounting exec default start-stop group radius
```

Configuring TACACS+:
```
R1(config)# tacacs-server host 192.168.1.100 key mytacacskey
R1(config)# aaa new-model
R1(config)# aaa authentication login default group tacacs+ local
R1(config)# aaa authorization exec default group tacacs+ local
R1(config)# aaa accounting exec default start-stop group tacacs+
```
### RADIUS vs TACACS+ comparison table

| Feature                | RADIUS                                      | TACACS+                                  |
|------------------------|---------------------------------------------|------------------------------------------|
| **Protocol Type**       | UDP (faster, less reliable)                | TCP (slower, more reliable)              |
| **Encryption**          | Encrypts only the password                 | Encrypts the entire packet               |
| **Authentication**      | Combined authentication and authorization  | Authentication, authorization, and accounting are separate |
| **Command Authorization** | Limited (based on group permissions)     | Granular (command-level control)         |
| **Accounting**          | Supported (track usage)                    | Supported (detailed session logs)        |
| **Scalability**         | More scalable for large numbers of users   | Less scalable due to TCP overhead        |
| **Configuration**       | Easier to configure                        | More complex to configure                |
| **Best Use Case**       | Network access (Wi-Fi, VPN, dial-up)       | Network administration (router, switch command access) |
