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
- `man-db`
- `networkmanager`
- `amd-ucode` (for amd cpu)
- `openssh`
- `pacman-contrib`  (for rankmirrors)
- `git`
- `sudo`
- `vi`
- `vim`
- `neovim`

## 3. Configure the system
`$ pacman-key --init`

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

# Post Installation
## Network Manager
See [NetworkManager](https://wiki.archlinux.org/title/NetworkManager).
```sh
$ systemctl start NetworkManager
$ systemctl enable NetworkManager
$ nmcli device wifi list
$ nmcli device wifi connect <SSID> password <password>
```

## Select the mirrors
See [Mirrors](https://wiki.archlinux.org/title/Mirrors).  
Get up-to-date some local mirrors.
```sh
$ rankmirrors -n 0 -v /etc/pacman.d/mirrorlist-backup > /etc/pacman.d/mirrorlist
$ pacman -Syyuu
```

## Time synchronization
See [systemd-timesyncd](https://wiki.archlinux.org/title/Systemd-timesyncd).
```sh
$ systemctl start systemd-timesyncd
$ systemctl enable systemd-timesyncd
# edit /etc/systemd/timesyncd.conf
$ systemctl restart systemd-timesyncd
$ timedatectl set-ntp true
```

## Grub and dual-booting
```sh
$ os-prober  # should see windows detected
$ grub-mkconfig -o /boot/grub/grub.cfg
```

## Hibernate
See [Power management/Suspend and hibernate](https://wiki.archlinux.org/title/Power_management/Suspend_and_hibernate#Pass_hibernate_location_to_initramfs).   
Edit `/etc/mkinitcpio.conf`.
- add `resume` to `HOOKS=(...)`
- `$ mkinitcpio -P`
Edit `/etc/default/grub`
- add `resume=UUID=<UUID of swap>` to `GRUB_CMDLINE_LINUX_DEFAULT="..."`
- `$ grub-mkconfig -o /boot/grub/grub.cfg`

## Add user
See [User management](https://wiki.archlinux.org/title/Users_and_groups#User_management) and [sudo](https://wiki.archlinux.org/title/sudo).
```sh
$ useradd -m -s <shell> <username>
$ passwd <username>
$ visudo  # enable "sudo" group and add "Defaults passwd_timeout=0"
$ groupadd sudo
$ usermod -aG sudo <username>
```

# Hyprland
See [Hyprland (arch wiki)](https://wiki.archlinux.org/title/Hyprland) and [Master tutorial (hyprland wiki)](https://wiki.hyprland.org/Getting-Started/Master-Tutorial/)
```sh
$ pacman -S hyprland kitty
# For nvidia gpu
    $ pacman -S linux-headers nvidia-dkms nvidia-uitils egl-wayland polkit gtk3
    # edit /etc/pacman.conf
        # uncomment [multilib]
        # uncomment Include = /etc/pacman.d/mirrorlist
    $ pacman -Syu
    $ reboot
    $ pacman -S lib32-nvidia-utils libva-nvidia-driver
    # edit /etc/default/grub
        # add `nvidia_drm.modeset=1` to GRUB_CMDLINE_LINUX_DEFAULT
        # [Notice: no "nvidia."]
        # add NVreg_PreserveVideoMemoryAllocations=1 to GRUB_CMDLINE_LINUX_DEFAULT
    $ grub-mkconfig -o /boot/grub/grub.cfg
    # edit /etc/mkinitcpio.conf
        # add `nvidia nvidia_modeset nvidia_uvm nvidia_drm` to MODULES
    $ mkinitcpio -P
    # edit /usr/share/hyprland/hyprland.conf
        # add the following lines
        env = LIBVA_DRIVER_NAME,nvidia
        env = XDG_SESSION_TYPE,wayland
        env = GBM_BACKEND,nvidia-drm
        env = __GLX_VENDOR_LIBRARY_NAME,nvidia
        env = NVD_BACKEND,direct
        cursor {
            no_hardware_cursors = true
        }
    # For suspend & hibernate
    $ systemctl enable nvidia-suspend.service
    $ systemctl enable nvidia-hibernate.service
    $ systemctl enable nvidia-resume.service
    $ reboot
# End for nvidia gpu
$ Hyprland
# paste the added lines in hyprland.conf to ~/.config/hypr/hyprland.conf
```

## Must have
See [must have](https://wiki.hyprland.org/Useful-Utilities/Must-have/).

### A notification daemon
```sh
$ pacman -S swaync
$ cp /etc/xdg/swaync/config.json ~/.config/swaync/config.json
$ swaync-client --reload config
# edit hyprland.conf
    # add `swaync` to "exec-once = ..."
```

### Pipewire
```sh
$ pacman -S pipewire wireplumber
```

### XDG Desktop Portal
```sh
$ pacman -S xdg-desktop-portal-hyprland
```

### Authentication Agent
```sh
$ pacman -S polkit-kde-agent
# edit hyprland.conf
    # add `/usr/lib/polkit-kde-authentication-agent-1` to `exec-once = ...`
```

### Qt Wayland Support
```sh
$ pacman -S qt5-wayland qt6-wayland
```