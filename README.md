Installing
=

#### Arch linux on a Lenovo X230 (version CPU 3210M) notebook.

### Creating USB Boot Media:
```
Download the arch-XXX-dual.iso image from https://archlinux.org/download/

Use Rufus | Etcher | etc.
```

## Boot from USB

    Ensure Secure Boot is disabled in the BIOS.
    Hit ENTER on BIOS prompt, hit F12, select USB drive.
    In the menu, select Arch Linux BIOS.

# Connect to Network

**Either plug in the Ethernet cable, or to get access via WiFi:**


#### Run command:
```
> ip link 

> rfkill unblock wifi

> ip link set wlan0 up (Choose your interface. For example: wlan0, wlp3s0...)

> iwctl

> station wlan0 connect <SSID>

> Passhprase:enter your password
 ```  
## Partitioning

We will use the following partition layout using the parted and gdisk/fdisk commands:

