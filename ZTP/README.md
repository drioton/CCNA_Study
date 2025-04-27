# ZTP (Zero Touch Provisioning) in Cisco
ZTP (Zero Touch Provisioning) is a process that allows network devices like switches, routers, or other devices to be automatically configured and provisioned without requiring manual intervention from the administrator. This process is often used when deploying devices in a network to simplify and speed up the configuration process.

Key Information About ZTP in Cisco
ZTP Goal:
The goal of ZTP is to enable network devices (e.g., Cisco switches or routers) to automatically download and apply necessary configuration files from a server without manual intervention.

## ZTP Process:

Device connects to the network.

Device powers up and, after initialization, attempts to contact the ZTP server (via DHCP or another mechanism).

ZTP server provides the device with the configuration file, which is applied automatically.

Device configures itself based on the configuration file and is ready for use.

ZTP and DHCP: ZTP typically uses the DHCP (Dynamic Host Configuration Protocol) to obtain information about the ZTP server. The device can get an IP address and the address of the server from which the configuration file will be downloaded. DHCP can also provide other options, such as the TFTP server or HTTP server for delivering configuration files.

Cisco Device Support: Cisco provides support for ZTP in some of its devices, such as Cisco Catalyst or Cisco IOS-XE devices. These devices can be configured to use ZTP for automatic setup when powered on for the first time.

## Use Cases:

Deploying new devices in the network: ZTP is especially useful when deploying new devices because it allows administrators to quickly deploy large numbers of devices without needing manual configuration.

Centralized Management: Devices can be configured and updated via a central ZTP server, saving time and reducing errors.

## Security Considerations:

It's important to secure ZTP by using proper authentication and encryption for the configuration files, as devices can be vulnerable if they receive incorrect or unauthorized configurations.

## Example of ZTP Configuration on a Cisco Device:
On a Cisco device (e.g., Catalyst), an administrator can set up ZTP using the following steps:

Enable ZTP on the Cisco device:

```
Switch# configure terminal
Switch(config)# ztp mode automatic
```

### Configure the ZTP server via DHCP:

On the DHCP server, you can set Option 66, which points to the TFTP or HTTP server from which the device will download its configuration.

Testing ZTP: After rebooting the device and connecting it to the network, the device will automatically download the configuration file from the server configured during the ZTP process.

## Advantages of ZTP:
Quick deployment of new devices in the network.

Reduced errors in manual configuration of devices.

Centralized configuration management, where updates are done from a single location.

Scalability – ZTP is efficient for deploying a large number of devices.

## Conclusion:
ZTP is a highly useful tool for automating the deployment of devices in networks, especially in large and dynamic installations. For network administrators, it provides an efficient way to save time and minimize the risk of errors during manual device setup.

# Setting Up ZTP on a Cisco Switch
Configure the DHCP Server: On the DHCP server, you need to set up Option 66 to point to the TFTP server where the configuration files will be stored.

Example configuration for a Linux-based DHCP server:

```
option tftp-server-name "192.168.1.100";  # TFTP server IP
option bootfile-name "switch-config.txt";  # Name of the configuration file
```

Prepare the TFTP Server: On the TFTP server, place the configuration file (e.g., switch-config.txt) that the switch will download.

Example of the content of switch-config.txt:

### Cisco Switch Configuration for ZTP
```
hostname Switch1
interface GigabitEthernet1/0/1
  description Connected to Router
  switchport mode access
  switchport access vlan 10
  ```
Set Up the Cisco Switch: On the Cisco switch, you need to enable ZTP mode.

### Enable ZTP on the Cisco Switch:

```
Switch# configure terminal
Switch(config)# ztp mode automatic
```
This will allow the switch to automatically attempt to contact the DHCP server and download its configuration file.

<b>Verify the Configuration:</b> Once the switch is powered on and connected to the network, it should automatically receive its IP address from the DHCP server and get the configuration file from the TFTP server.

### Check if the configuration is applied:
```
Switch# show running-config
```
This command should show the configuration that was downloaded from the TFTP server.

### Summary of the Steps
Configure DHCP Option 66 on the DHCP server to point to the TFTP server.

Place the configuration file (e.g., switch-config.txt) on the TFTP server.

Enable ZTP on the Cisco switch using the command:
```
Switch(config)# ztp mode automatic
```
Verify that the switch receives the correct configuration after reboot.