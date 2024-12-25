---
layout: '../layouts/Base.astro'
---



<img src="https://archlinux.org/static/logos/archlinux-logo-dark-1200dpi.png" alt="Arch Linux Logo" width="300" style="position: relative; top: 16px;"> [Installation Guide](/about)

 #### This document provides a comprehensive step-by-step guide to install Arch Linux, formatted in Markdown for easy reading and sharing.

---

## Table of Contents

1. [Preparation](#preparation)
2. [Booting from Installation Media](#booting-from-installation-media)
3. [Setting Up the Environment](#setting-up-the-environment)
4. [Partitioning the Disk](#partitioning-the-disk)
5. [Formatting and Mounting Partitions](#formatting-and-mounting-partitions)
6. [Installing the Base System](#installing-the-base-system)
7. [Configuring the System](#configuring-the-system)
8. [Setting Up the Bootloader](#setting-up-the-bootloader)
9. [Post-Installation](#post-installation)

---

## Preparation

### 1. Download the Arch Linux ISO
- Visit the [Arch Linux Downloads](https://archlinux.org/download/) page.
- Download the ISO file.

### 2. Create Bootable Media
Use tools like `dd`, Rufus, or Balena Etcher to create a bootable USB.

#### Example with `dd`:
```bash
sudo dd if=archlinux.iso of=/dev/sdX bs=4M status=progress && sync
```
Replace `/dev/sdX` with your USB device.

---

## Booting from Installation Media

1. Boot your system from the USB.
2. Select **Boot Arch Linux** from the menu.

---

## Setting Up the Environment

### 1. Check Internet Connection
```bash
ping archlinux.org
```
If using Wi-Fi, connect using `iwctl`:
```bash
iwctl
> device list
> station <device> connect <SSID>
```

### 2. Update System Clock
```bash
timedatectl set-ntp true
```

---

## Partitioning the Disk

### 1. List Disks
```bash
fdisk -l
```

### 2. Partition Disk with `fdisk`
```bash
fdisk /dev/sdX
```
Create partitions:
- EFI System Partition (512MB, type `EFI`)
- Linux Filesystem Partition (remaining space)

---

## Formatting and Mounting Partitions

### 1. Format Partitions
- EFI Partition:
```bash
mkfs.fat -F32 /dev/sdX1
```
- Linux Filesystem Partition:
```bash
mkfs.ext4 /dev/sdX2
```

### 2. Mount Partitions
```bash
mount /dev/sdX2 /mnt
mkdir /mnt/boot
mount /dev/sdX1 /mnt/boot
```

---

## Installing the Base System

### 1. Install Base Packages
```bash
pacstrap /mnt base linux linux-firmware
```

### 2. Generate Filesystem Table
```bash
genfstab -U /mnt >> /mnt/etc/fstab
```

---

## Configuring the System

### 1. Change Root
```bash
arch-chroot /mnt
```

### 2. Set Timezone
```bash
ln -sf /usr/share/zoneinfo/Region/City /etc/localtime
hwclock --systohc
```

### 3. Localization
Edit `/etc/locale.gen` and uncomment your locale (e.g., `en_US.UTF-8 UTF-8`):
```bash
locale-gen
```
Create `/etc/locale.conf`:
```bash
echo "LANG=en_US.UTF-8" > /etc/locale.conf
```

### 4. Set Hostname
```bash
echo "myhostname" > /etc/hostname
```
Edit `/etc/hosts`:
```bash
127.0.0.1   localhost
::1         localhost
127.0.1.1   myhostname.localdomain myhostname
```

### 5. Set Root Password
```bash
passwd
```

---

## Setting Up the Bootloader

### 1. Install Bootloader (GRUB)
```bash
pacman -S grub efibootmgr
```

### 2. Install GRUB
```bash
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB
```

### 3. Generate GRUB Configuration
```bash
grub-mkconfig -o /boot/grub/grub.cfg
```

---

## Post-Installation

1. Unmount Partitions:
```bash
umount -R /mnt
```

2. Reboot System:
```bash
reboot
```

3. Remove Installation Media during boot.

---

Congratulations! You have successfully installed Arch Linux.
