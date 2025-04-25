# JSON + REST API

## What is JSON?
JSON = JavaScript Object Notation

It’s a lightweight data format used to exchange data between systems ( between script and a router).

It's written as key: value pairs.

```{
  "hostname": "Router1",
  "interface": "GigabitEthernet0/1",
  "status": "up"
}
```

## JSON SYNTAX – What Each Part Means


| Concept   | Description                                                              |
|-----------|--------------------------------------------------------------------------|
| `{ }`     | Represents an object (like a dictionary in Python)                       |
| `[ ]`     | Represents an array (like a list in Python)                              |
| `key`     | The name in a key-value pair (always a string)                           |
| `value`   | The data paired with the key (string, number, boolean, object, or array) |
| `:`       | Separates the key from the value                                          |
| `,`       | Separates items or key-value pairs                                       |
| string    | Text inside double quotes, e.g., `"hello"`                               |
| number    | A numeric value (e.g., `42`, `3.14`) without quotes                      |
| boolean   | Either `true` or `false` (not in quotes)                                 |


- Example

```{
  "hostname": "Router1",
  "interface": {
    "name": "GigabitEthernet0/1",
    "status": "up",
    "ip_address": "192.168.1.1"
  },
  "enabled": true,
  "vlans": [10, 20, 30]
}
```

 ## 1. What is an Object?
An object is a set of key-value pairs enclosed in { }.

```
{
  "router": "R1",
  "location": "Data Center"
}
```

## 2. What is an Array?
An array is a list of items enclosed in [ ].

```
{
  "vlans": [10, 20, 30]
}
```
## 3. What is REST API?

REST API = Representational State Transfer API
It’s a way for programs to communicate over the network using HTTP requests like:
| Method | Description                           |
|--------|---------------------------------------|
| GET    | Retrieve data                         |
| POST   | Create a new resource                 |
| PUT    | Update/replace an existing resource   |
| DELETE | Remove a resource                     |

## 4. Basic REST API Example - Cisco Interface Setup

Target: Turn on interface GigabitEthernet1 on device

- GET (Check current status)
```
GET /api/v1/interface/GigabitEthernet1
```
- POST (Create config if needed)
```
POST /api/v1/interface
{
  "name": "GigabitEthernet1",
  "description": "Uplink to Switch",
  "enabled": true
}
```

- PUT (Update config)
```
PUT /api/v1/interface/GigabitEthernet1
{
  "enabled": true
}
```
-  DELETE (Shutdown/remove interface config)
```
DELETE /api/v1/interface/GigabitEthernet1
```
## 5. Example API Response

```{
  "status": "success",
  "interface": {
    "name": "GigabitEthernet1",
    "enabled": true
  }
}
```
## 6. What is DevNet Sandbox?


The DevNet Sandbox is a cloud-based platform from Cisco that lets you test and experiment with Cisco devices, networks, and APIs without needing any physical hardware. It's free to use and provides a safe space for you to practice with different Cisco technologies.

- Key Points:
Free Access to real Cisco equipment, like routers, switches, and firewalls, but all virtualized.

Pre-configured Labs for things like SD-WAN, automation, and security.

- Hands-on Learning: You can use it to practice commands, test APIs, and automate network tasks.

- No Hardware Needed: You don’t have to buy anything or worry about managing devices.

- Easy to Start: Just sign up on the DevNet website, pick a sandbox, and you’re good to go.

- How It Works:
Sign Up: You create an account on the Cisco DevNet site.

- Choose a Sandbox: Pick a sandbox based on what you want to learn, like SD-WAN or network automation.

- Start Testing: Once you’re inside, you can play around with configurations, make API calls, or practice automation.

- Common Use Cases:
- Learning: Great for practice when studying for Cisco certifications.

- Network Automation: You can write scripts to automate tasks like configuring routers.

- API Testing: Experiment with different Cisco APIs (for example, making GET or POST requests).

## 7. What is Netmiko?
Netmiko is a Python library that simplifies connecting to Cisco routers/switches via SSH.

```
from netmiko import ConnectHandler

device = {
    'device_type': 'cisco_ios',
    'host': '192.168.1.1',
    'username': 'admin',
    'password': 'cisco'
}

conn = ConnectHandler(**device)
output = conn.send_command("show ip int brief")
print(output)
```
## 8. YAML
YAML is another format like JSON but more human-readable. Used in configs like Ansible, Kubernetes.

| Concept  | Description                                                                 |
|----------|-----------------------------------------------------------------------------|
| YAML     | YAML Ain't Markup Language. It is a human-readable data serialization format used for configuration files. |
| Structure| It uses indentation (usually 2 spaces) to define data structures like objects and arrays. |
| Syntax   | YAML syntax is simple, using key-value pairs. No need for quotes around strings, except for special characters. |
| Data Types | Supports strings, numbers, arrays (lists), objects (key-value pairs), and booleans. |
| Common Use | Configuration files (e.g., Kubernetes, Docker Compose, Ansible, etc.). |

## 9. Summary of HTTP Methods with Cisco API
| Method  | Description                                                        |
|---------|--------------------------------------------------------------------|
| GET     | Retrieve data from the server (e.g., get current device status).   |
| POST    | Create a new resource (e.g., create a new user or device config).  |
| PUT     | Update an existing resource (e.g., modify the configuration).      |
| DELETE  | Remove a resource (e.g., delete a user or device setting).         |

## 10. Full REST API Example (Cisco)

| HTTP Method | Example API Endpoint                          | Description                                               | Example Data (JSON)                                                    |
|-------------|-----------------------------------------------|-----------------------------------------------------------|------------------------------------------------------------------------|
| GET         | `/api/v1/device`                              | Retrieve current device configuration.                    | `{ "device": "router1", "status": "active" }`                           |
| POST        | `/api/v1/device/config`                       | Create a new device configuration.                        | `{ "name": "router1", "ip": "192.168.1.1", "model": "C9200" }`         |
| PUT         | `/api/v1/device/config`                       | Update an existing device configuration.                  | `{ "name": "router1", "ip": "192.168.1.2" }`                           |
| DELETE      | `/api/v1/device/{device_id}`                  | Remove a device from the configuration.                   | N/A (no body required)                                                 |


## 11. Test

### 1. What is the basic syntax structure of a JSON object?

A) []

B) {}

C) ""

D) ()

### 2. In a JSON object, what does the colon (:) represent?

A) It separates the key from the value.

B) It separates different JSON objects.

C) It separates arrays.

D) It indicates the end of an object.

### 3. Which of the following is a valid JSON key-value pair?

A) "name": "router1"

B) name: router1

C) key->"value"

D) [key, value]

### 4. What data types are supported in JSON?

A) Integer, String, Boolean

B) String, Array, Boolean, Null, Object, Number

C) Integer, String, Array, Date

D) String, List, Integer, Double

### 5. What is the correct way to represent an array in JSON?

A) ()

B) []

C) {}

D) ""

### 6. What will the following JSON represent?

```
{
  "hostname": "router1",
  "ip": "192.168.1.1"
}
```
A) An array with two items.

B) An object containing the hostname and IP of a router.

C) A string with the name "router1" and "192.168.1.1".

D) A boolean value indicating "true".

### 7. How do you access a value in a JSON object using Python?

A) json["key"]

B) json.key()

C) json->key

D) json.get("key")

### 8. What does the following JSON snippet represent?

```
{
  "devices": [
    {"name": "router1", "ip": "192.168.1.1"},
    {"name": "switch1", "ip": "192.168.1.2"}
  ]
}
```
A) An array of devices with names and IP addresses.

B) A string with the name and IP address of the devices.

C) A single object with multiple keys.

D) An invalid JSON because arrays can’t contain objects.

### 9. What is the purpose of JSON in network management and automation for CCNA?

A) To represent network topology in graphical form.

B) To define configurations and settings in a machine-readable format.

C) To track packet flows through a network.

D) To monitor the physical layer of a network.

### 10. Which of the following is the correct syntax to define a Boolean value in JSON?

A) "true"

B) "false"

C) true

D) False

# 12. Answer

### 1. What is the basic syntax structure of a JSON object?

Answer: B) {}

### 2. In a JSON object, what does the colon (:) represent?

Answer: A) It separates the key from the value.

### 3. Which of the following is a valid JSON key-value pair?

Answer: A) "name": "router1"

### 4. What data types are supported in JSON?

Answer: B) String, Array, Boolean, Null, Object, Number

### 5. What is the correct way to represent an array in JSON?

Answer: B) []

### 6. What will the following JSON represent?
```
{
  "hostname": "router1",
  "ip": "192.168.1.1"
}
```
Answer: B) An object containing the hostname and IP of a router.

### 7. How do you access a value in a JSON object using Python?

Answer: A) json["key"]

### 8. What does the following JSON snippet represent?

```
{
  "devices": [
    {"name": "router1", "ip": "192.168.1.1"},
    {"name": "switch1", "ip": "192.168.1.2"}
  ]
}
```
Answer: A) An array of devices with names and IP addresses.

### 9. What is the purpose of JSON in network management and automation for CCNA?

Answer: B) To define configurations and settings in a machine-readable format.

### 10. Which of the following is the correct syntax to define a Boolean value in JSON?

Answer: C) true






















