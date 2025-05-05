# Cisco IOS – File System, FTP, Boot, Hashes, Updates, and Security

## How Cisco IOS Starts (Boot Process)
When a Cisco device boots, it goes through the following steps:

<b>1. Power-On Self Test (POST)</b>
Basic hardware diagnostics.

Checks CPU, RAM, flash, interfaces.

<b>2. ROMMON (ROM Monitor)</b>
Low-level OS used for recovery and manual boot.

If no valid IOS is found, it drops to rommon> mode.

<b>3. Load IOS Image</b>
IOS is loaded from flash memory or another location.

Boot order is defined by the boot system command.

```
boot system flash:c1900-universalk9-mz.SPA.154-3.M3.bin
```
<b>4. Load Configuration</b>
If IOS loads correctly, it looks for startup-config in NVRAM.

If not found, the router enters setup mode.

<b>5. Run IOS</b>
IOS runs from RAM, loaded into memory from flash: or bootflash:.

##  Where Is the IOS System?

| File System                                     | Description                                                       |
| ----------------------------------------------- | ----------------------------------------------------------------- |
| `flash:`                                        | Main location of IOS images.                                      |
| `bootflash:`                                    | Used in some devices (switches, newer routers).                   |
| `system:`                                       | Virtual file system showing the **currently running IOS in RAM**. |
| Used mostly for display and internal reference. |                                                                   |

Show system image path:
```
show version
```
Sample output:

```
System image file is "flash:c1900-universalk9-mz.SPA.154-3.M3.bin"
```

## File System Navigation
```
dir flash:
dir bootflash:
more flash:config.txt
```

## Transfer IOS via FTP or TFTP

Set FTP credentials
```
ip ftp username admin
ip ftp password secret123
```

Transfer IOS
```
copy ftp: flash:

```
You can also use
```
copy tftp: flash:
copy usbflash0: flash:
```

## Hash Verification

Check image integrity
```
verify /md5 flash:ios-image.bin
verify /sha256 flash:ios-image.bin   # if supported

```
Compare with Cisco-provided hash to ensure no corruption or tampering.

## IOS Upgrade Procedure


1. Upload new IOS to flash

2. Verify hash

3. Set new boot image:

```
conf t
boot system flash:new-ios.bin
end
```
4. Save config:
```
write memory
```
5. Reload device:

```
reload
```

After reboot:

```
show version
```

## IOS Image Security
- Verify hash (MD5/SHA)

- Use secure transfer (FTP with login or SCP)

- Keep old IOS in flash as backup

- Restrict physical access to console

- Use boot password / secure ROMMON

## Summary

| Component     | Function                                   |
| ------------- | ------------------------------------------ |
| `flash:`      | Stores IOS, accessible at boot             |
| `system:`     | Shows running image loaded into RAM        |
| `nvram:`      | Holds startup-config                       |
| `rommon>`     | Used if IOS fails to load, for recovery    |
| `boot system` | Command to define which image to boot from |
| `verify`      | Validates image integrity via hash         |

# Essential things about Cisco IOS and system management

### 1. Configuration Files
- startup-config → Stored in NVRAM, used at boot.

- running-config → Stored in RAM, changes are not permanent until saved.

<b>Useful commands:</b>

```
copy running-config startup-config
copy startup-config running-config
erase startup-config
```
### 2. Recovering from Missing or Corrupt IOS
You’ll fall into ROMMON if the image is missing or corrupt.

You can use xmodem, TFTP or USB to load a new IOS manually.

Example recovery:
```
rommon 1 > tftpdnld
```
Or copy via USB (if supported):
```
copy usb0:ios.bin flash:
```
### 3. Secure Boot and Image Signing (For CCNP but Good to Know)
Newer IOS versions support image signing and secure boot:

```
secure boot-image
secure boot-config
```
These protect against unauthorized image or config tampering.

### 4. Config Register (Important for Password Recovery and Boot Behavior)
Command:
```
show version
```
You’ll see:
```
Configuration register is 0x2102
```
<b>0x2102</b> – Normal boot from NVRAM config.

<b>0x2142</b> – Ignores startup-config (used in password recovery).

Set config-register:
```
conf t
config-register 0x2102
end
reload
```
### 5. Filesystem and Permissions (read-only, corrupt)
If you get errors like read-only file system, it may be:

+ Flash is full

+ Flash is locked

+ Flash corruption

Use:
```
format flash:
delete flash:*
```
Caution: formatting deletes <b>everything</b>.

### 6. FTP Passive vs Active Mode (when copying IOS)
<b>Passive:</b> Client opens all connections, works better through firewalls.

<b>Active:</b>Server opens data connection to client.

<b>Cisco IOS FTP client defaults to passive.</b>

You can’t force it via command but you can control it from server side (e.g. FileZilla).

### 7. Check Available Storage
```
dir flash:
dir usb0:
show flash:
```
### 8. Check IOS Features (if image supports crypto, IPsec, etc.)
```
show version | include image
```
Look for keywords like:

+ k9 = crypto-enabled

+ ipbase, ipservices, securityk9

### 9. Show and Debug for File Transfer
```
debug ip ftp
debug ip tftp
show file systems
```
### 10. Package Mode vs Bundle Mode (Catalyst switches)
Bundle Mode: Boot directly from .bin

Install Mode: Extracted .pkg files from .bin image

Command to verify:
```
show version | include mode
```
Convert to install mode:
```
install add file flash:cat9k...bin activate commit
```























