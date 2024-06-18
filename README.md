# Install Arch Linux
Follow [the installation guide](https://wiki.archlinux.org/title/Installation_guide). Take this as additional information.  
Information about my laptop:
- 2 SSDs.
- Have an existing Windows 10 on one SSD.
- Installing Arch on the other one.
- Use UEFI.
## 1. Pre-installation
### 1.9 Partition the disk
Use `$ fdisk` to create a gpt table with
- 1G `boot (EFI)`
- RAM-size `swap`
- the remainder as `Linux root`.
### 1.10 Format the partitions
Label `swap` partition as `swap`.
### 1.11 Mount the file systems
Also mount the boot partition for windows.
## 2. Installation
### 2.1 Select the mirrors
Edit `/etc/pacman.d/mirrorlist` later.
### 2.2 Install essential packages
Additional packages:
- `vim`
- `man-db`
- `networkmanager`
- `amd-ucode` (for amd cpu)

## 3. Configure the system
### 3.3 Time
Configure `systemd-timesyncd` later.
### 3.5 Network configuration
Add `127.0.0.1    localhost` and `::1    localhost` to `/etc/hosts`.  
Configure `networkmanager` later.
### 3.8 Boot loader
Follow the [grub wiki page](https://wiki.archlinux.org/title/GRUB#Windows_installed_in_UEFI/GPT_mode).
#### Grub configuration
Edit `/etc/default/grub`
- uncomment `GRUB_DISABLE_OS_PROBER=false`

Install grub on `/boot` and generate configuration file.