Installing
=

#### Arch linux on a Lenovo X230 (version CPU 3210M) notebook.

### Creating USB Boot Media:
```
Download the archlinux-xxxx.xx.xx-x86_64.iso  image from https://archlinux.org/download/

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
```
> fdisk -l
> fdisk /dev/<sda> (choose your name disk)
>> g
>> w
> cfdisk /dev/sda
New --> 300M -> Type: BIOS Boot
New --> 300M-500M -> Type: EFI system
New --> Free space (example: 118G) -> Type: Linux filesystem
Write -> yes -> Quit
```
## Create the Filesystems

Next, we need to format the partitions created in the previous step. I assume that you block device is /dev/sda and that the partitions were created as above.
```
> mkfs.vfat /dev/<sda> (EFI system)
> mkfs.btrfs /dev/<sda> (Linux filesystem)
```
## Mount the Partitions
```
> mount /dev/sda3 (linux system) /mnt
> mkdir /mnt/boot
> mount /dev/sda2 (EFI system) /mnt/boot
```

## Install System Files

With all the partitions mounted in place, we will now continue to install Arch Linux on the system. We install the base and base-devel collections:
```
pacstrap -i /mnt base base-devel linux-zen linux-zen-headers linux-firmware dosfstools btrfs-progs intel-ucode iucode-tool vim nano
```
After all packages are installed, we generate an fstab file. Here the -U flag asks the generator to use partition UUIDs instead of kernel device names.

genfstab -U /mnt > /mnt/etc/fstab

## Next, we switch into our new system and finish the installation there.
```
> arch-chroot /mnt 
> ln -sf /usr/share/zoneinfo/<choose your region>/... /etc/localtime
> hwclock --systohc
> vim /etc/locale.gen
```
In this file, find the line that corresponds to your language preference. Mine is US English with UTF-8 character support and russian RU with UTF-8, en_US.UTF-8 ru_RU.UTF-8. After editing, we let the system generate the locale files.
```
> locale-gen
> echo LANG=en_US.UTF-8 > /etc/locale.conf
> export LANG=en_US.UTF-8
```
ru keyboard
```
> vim /etc/vconsole.conf
> KEYMAP=ruwin_alt_sh_sh-UTF-8
> FONT=cyr-sun16
```
## Setup Hostname

In the next step, set the hostname for your machine.
```
> echo <arch> (example) > /etc/hostname
```
## Setup Hosts
```
> vim /etc/hosts
127.0.0.1 localhost
::1       localhost
127.0.0.1 <arch.localdomain> (example)  localhost
```
initramfs
```
> mkinitcpio -P
```
## Let’s set a password for our root user
```
> passwd
```
Add a regular user account that we will use in our day-to-day activities:
```
> useradd -m -G wheel,,users,lp /bin/bash <username>
> passwd <username>
> nano /etc/sudoers
uncomment -- %wheel ALL=(ALL) ALL
```

## Installing Grub
```
> pacman -S grub efibootmgr dhcpcd dhclient networkmanager wpa_supplicant ...
> grub-install /dev/<sda>
> grub-mkconfig -o /boot/grub/grub.cfg (configure grub)
```

Boot the new OS
```
> exit    
> umount -R /mnt
> reboot
```


