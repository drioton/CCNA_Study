
# Security Concepts in CCNA


| Term                  | Description                                                                 | Real-World Example                                                                 |
|-----------------------|-----------------------------------------------------------------------------|------------------------------------------------------------------------------------|
| DoS                   | One device floods a service with traffic                                    | A hacker sends endless pings to a web server, making it crash                      |
| DDoS                  | Multiple devices (botnet) flood the target                                  | 1000 infected PCs send traffic to a gaming server, making it unusable             |
| Amplification Attack  | Small request → big response sent to victim                                | Using DNS to send 60-byte request → 4KB response to victim                        |
| Reflection Attack     | Spoofed IP causes legit servers to reply to the victim                     | Attacker sends NTP requests with victim’s IP, victim gets flooded with replies    |
| Man-in-the-Middle     | Attacker intercepts/modifies communication                                  | Public Wi-Fi attacker sniffs login data between your phone and bank               |
| Malware               | Malicious software (virus, worm, etc.)                                      | You download a "free game" that steals your browser data                          |
| Buffer Overflow       | Too much data crashes app or gives attacker control                         | A login field accepts 500 chars → attacker injects shellcode                      |
| Exploit               | Code or method that abuses a vulnerability                                  | Exploit for unpatched Windows SMB lets hacker spread ransomware                   |
| Threat                | A potential cause of damage                                                  | An angry ex-employee with old VPN access                                          |
| Vulnerability         | Weakness in system security                                                 | Outdated Apache server allows known remote code execution                        |
| Phishing              | Fake email or site stealing login info                                       | “Your bank account was locked. Click here to reset password”                      |
| Whaling               | Phishing targeting high-level individuals                                   | Fake invoice email sent to CEO asking to wire €100,000                            |
| Pharming              | Redirects users to fake site via DNS tricks                                 | Typing `mybank.com` opens fake site that looks real                               |
| Watering Hole Attack  | Infecting a trusted site used by target group                               | Hacker hacks a software forum, adds malware to one download link                 |


### DoS (Denial of Service)
A DoS attack is a single-source flood attack. The goal is to overwhelm the target so it becomes slow or completely stops working. It usually exploits a resource limit, like bandwidth, CPU, or open ports.

+ Example: Sending continuous ICMP Echo Requests (ping) until a web server can’t handle real users anymore.

### DDoS (Distributed Denial of Service)
Same principle as DoS but launched from multiple infected systems (a botnet). Much harder to stop since traffic comes from many locations.

+ Example: Mirai botnet infected IoT devices and used them to take down major DNS services (like Dyn), affecting Twitter, Netflix, and others in 2016.

### Amplification Attack
The attacker sends a tiny request to a third-party server (like DNS), but fakes the source IP (the victim's IP). That server sends a much larger response to the victim, amplifying the attack traffic.

+ Example: Send a 60-byte DNS request that returns a 4000-byte response. Do that thousands of times – the victim gets overwhelmed.

### Reflection Attack
Similar to amplification, but here the attacker uses multiple legitimate servers to reflect their responses to the victim, making it harder to trace and block.

+ Example: Spoof the victim’s IP and send NTP requests to 1000 public NTP servers. All servers reply to the victim at once.

### Man-in-the-Middle (MitM)
The attacker sits between two devices and secretly intercepts or even changes the data. Can happen on unsecured Wi-Fi, or by ARP spoofing in a LAN.

+ Example: On public Wi-Fi, an attacker intercepts login credentials by spoofing the access point or using tools like ettercap.

### Malware
Short for “malicious software.” This can be viruses, worms, trojans, spyware, ransomware, etc. It typically steals data, opens backdoors, or encrypts files for ransom.

+ Example: A fake Adobe Flash update installs ransomware that encrypts your files and asks for Bitcoin to unlock them.

### Buffer Overflow
An attacker sends more data than an application expects, overflowing the buffer and overwriting memory, often allowing code execution.

+ Example: Inputting 3000 "A" characters into a login field crashes the app or gives attacker shell access.

### Exploit
A specific technique or piece of code that takes advantage of a vulnerability to gain control or disrupt systems.

+ Example: EternalBlue exploit used by WannaCry ransomware to spread through Windows systems.

### Threat
A threat is anything that can cause harm to a system – attackers, natural disasters, human error.

+ Example: An insider with administrative access who’s unhappy and wants to leak company data.

### Vulnerability
A weakness in software, hardware, or configurations that can be used by attackers.

+ Example: Using Telnet instead of SSH is a vulnerability because it sends passwords in plain text.

### Phishing
Fake communication (usually email) that tricks users into revealing information.

+ Example: “Your PayPal account is locked. Click here to verify.” The link leads to a fake login page.

### Whaling
Same as phishing, but targets high-profile people like CEOs or system admins.

+ Example: Fake email from "the CEO" to the finance department asking for urgent wire transfer.

### Pharming
The victim is redirected to a malicious website even if they type the correct domain name.

+ Example: DNS cache poisoning redirects users from gmail.com to a fake login site.

### Watering Hole Attack
The attacker compromises a website that the target group often visits, and waits for the victim to get infected.

+ Example: Hackers inject malware into a popular vendor’s software download page used by tech companies.



# Scenario Mitigating a Simple DoS Attack

### Goal:
Block ICMP flood (DoS) from a known source IP using an Access Control List (ACL).

### Topology:
```
[Attacker PC: 192.168.1.100] ---> [Router] ---> [Server: 192.168.2.200]
```
ACL Configuration on the Router:
```
R1(config)# access-list 100 deny icmp host 192.168.1.100 any
R1(config)# access-list 100 permit ip any any
R1(config)# interface G0/0
R1(config-if)# ip access-group 100 in
```
This blocks ICMP from the attacker while allowing all other traffic.

### Diagram – Amplification & Reflection Attack

                 +---------------------+
                 |     Attacker        |
                 | Spoofs Victim's IP  |
                 +----------+----------+
                            |
             Spoofed Requests (e.g., DNS, NTP)
                            |
              +-------------+-------------+
              |             |             |
        +-----v-----+ +-----v-----+ +-----v-----+
        |  Server A | |  Server B | |  Server C |
        +-----------+ +-----------+ +-----------+
              |             |             |
        Large Replies to Victim’s IP (Reflection)
              \_____________|_____________/
                            |
                   +--------v--------+
                   |    Victim       |
                   | Overloaded w/   |
                   | huge responses  |
                   +----------------+
