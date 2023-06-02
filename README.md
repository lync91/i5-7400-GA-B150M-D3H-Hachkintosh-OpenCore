## Configuration

| Specifications | Detail |
| -------------- | ------ |
| Processor | Intel Core i5-7400 |
| Memory | 16GB DDR4 2400MHz |
| Motherboard | Gigabyte B150M D3H |
| Hard Disk | Kingston SSD SATA 120GB |
| Integrated Graphics | Intel HD Graphics 630 |

## What work ?

| Service | State |
| ------- | ----- |
| HDMI | Working at 1920x1080@60Hz |
| DVI | Working at 1920x1080@60Hz |
| Ethernet | Working at full speed |
| iGPU | Working |
| DRM | Working partially ([see more info](https://github.com/AurelienAudero/Intel-i5-7400-Hackintosh-EFI/issues/5)) |
| Wi-Fi | Working at full speed |
| Bluetooth | Soon |
| USB | Working at full speed (up to USB 3.1) |
| iCloud | Working |
| iMessage and FaceTime | Soon |
| Handoff and Continuity | Soon |
| Sleep | Not Working |

## Installation

### Make the Installation USB

Download [balenaEtcher](https://www.balena.io/etcher/) and the [macOS Ventura 13.4 Image](https://www.mediafire.com/file/7nwiwc08e1tbsjg/Olarila+Ventura+13.4.raw/file) .

Open balenaEtcher, select the `.raw` image you downloaded earlier, select the USB you want to use and click "Flash".

Open the EFI of your USB and **delete everything** (the root of the EFI should be blank).

Now, [download the latest version of this EFI](https://github.com/AurelienAudero/Intel-i5-7400-Hackintosh-EFI/releases/latest) and paste the "EFI" folder at the root of the USB it should look like this :

![EFI-directory-Screenshot](/Images/EFI-directory-Screenshot.png)

Finally, your USB is ready but before using it you will have to [Setup the SMBIOS](#setup-the-smbios)

**⚠️ It will not boot if you skip this part, so make sure to follow the following steps carefully**

### Setup the SMBIOS

To use this EFI, you will need to setup the SMIOS to match your configuration.

| SMIOS | Hardware |
| ----- | -------- |
| iMac18,1 | Used for computers utilizing the iGPU for displaying |
| iMac18,3 | Used for computers using a dGPU for displaying, and an iGPU for computing tasks only |
| Macmini8,1 | Used for computers having issues with iMac18,1 and that are utilizing the iGPU for displaying |

Download [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS/archive/refs/heads/master.zip) and extract it.

If you're on Windows, execute "GenSMBIOS.bat".

If you're on macOS, execute "GenSMBIOS.command".

**NOTE : You need to have the [latest version of Python](https://www.python.org/downloads/) installed to run this program.**

Once the script is running, pick the option 1 for downloading MacSerial.

After that, pick option 3 for selecting the SMBIOS and enter `iMac18,1` or `iMac18,3` or `Macmini8,1` (case sensitive) according to your configuration.

This will give you an output similar to the following : 

```
  #######################################################
 #               iMac18,1 SMBIOS Info                  #
#######################################################

Type:         iMac18,1
Serial:       C02FG0D5H7JY
Board Serial: C02112270CDH69FAD
SmUUID:       75609BAC-B4BF-4DC0-8D98-E09177474DB8
Apple ROM:    90B21FCE4687
```

The `Type` part gets copied to `Generic -> SystemProductName`.

The `Serial` part gets copied to `Generic -> SystemSerialNumber`.

The `Board Serial` part gets copied to `Generic -> MLB`.

The `SmUUID` part gets copied to `Generic -> SystemUUID`.

Don't use the `Apple ROM` part !

For `Generic -> ROM`, we use the MAC Address of the network interface, in all lowercase, and without `:`

**For example :**
- **MAC :** `00:16:CB:00:11:22`
- **ROM :** `0016cb001122`

**ℹ️ Remember to look on [Apple Check Coverage](https://checkcoverage.apple.com/) and you want to get this message back : "We’re sorry, we’re unable to check coverage for this serial number". If it's not the case, you need to regenerate another serial.**

**ℹ️ If you choose the `Macmini8,1` SMBIOS, it's recommanded to change the value in `Misc -> Security -> SecureBootModel` to `j174` in the config.plist**

**⚠️ NOTE : You and you alone are responsible for your Apple ID, read the guide carefully and take full responsibility if you screw up. Dortania, me and any other guides are not held accountable for what you do.**

### Setup the BIOS
**ℹ️ NOTE : All of these options may not be present in your BIOS, it is recommended to match as closely as possible but don't be too concerned if many of these options are not available in your BIOS.**

| ❌ You should disable | ✅ You should enable |
|-----------------------|----------------------|
| Fast Boot             | VT-x                 |
| Secure Boot           | Above 4G Decoding    |
| Serial/COM Port       | Hyper-Threading      |
| Parallel Port         | Execute Disable Bit  |
| VT-d                  | EHCI/XHCI Handoff    |
| CSM                   |                      |
| Thunderbolt           |                      |
| Intel SGX             |                      |
| Intel Platform Trust  |                      |
| CFG Lock              |                      |

|                🛠️ Settings you should change              |
|-----------------------------------------------------------|
| **OS Type :** `Windows 8.1/10 UEFI Mode` (or `Other OS`)  |
| **DVMT Pre-Allocated (iGPU Memory) :** `128MB` or higher  |
| **SATA Mode :** `AHCI`                                    |