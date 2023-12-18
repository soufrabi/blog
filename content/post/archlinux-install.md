---
title: "Archlinux Install" # Title of the blog post.
date: 2023-09-18T13:29:39+05:30 # Date of post creation.
description: "Article description." # Description used for search engine.
featured: true # Sets if post is a featured post, making appear on the home page side bar.
draft: false # Sets whether to render this page. Draft of true will not be rendered.
toc: false # Controls if a table of contents should be generated for first-level links automatically.
# menu: main
usePageBundles: false # Set to true to group assets like images in the same folder as this post.
featureImage: "/images/path/file.jpg" # Sets featured image on blog post.
featureImageAlt: 'Description of image' # Alternative text for featured image.
featureImageCap: 'This is the featured image.' # Caption (optional).
thumbnail: "/images/path/thumbnail.png" # Sets thumbnail image appearing inside card on homepage.
shareImage: "/images/path/share.png" # Designate a separate image for social media sharing.
codeMaxLines: 10 # Override global value for how many lines within a code block before auto-collapsing.
codeLineNumbers: false # Override global value for showing of line numbers within code block.
figurePositionShow: true # Override global value for showing the figure label.
categories:
  - Technology
tags:
  - archlinux
# comment: false # Disable comment if false.
---

Guide to install archlinux with minimal configuration
<!--more-->

### Connect to wifi

Enter iwd console
```sh
iwctl
```

Inside the iwd console
```
device list
station wlan0 scan
station wlan0 get-networks
station wlan0 connect "SSID_value"
```



### Partiion

Use fdisk or cfdisk to partition the drives

fdisk options

for FAT32 partiton set the type to 1. EFI
using `t`

Assign filesystem types
```sh
mkfs.fat -F32 /dev/sda1
mkfs.ext4 /dev/sda2
```


After partitioning is complete the directory structure should look like this
```
.
└── mnt
    ├── boot
    │   └── efi
    ├── etc
    │   └── fstab
    └── home
```

Use genfstab command to generate `/etc/fstab`
```sh
genfstab -U /mnt >> /mnt/etc/fstab

# Check using
cat /mnt/etc/fstab
```

### Keyring
For archlinux keyring use `pacman-key` command

To refresh some or all keys
```sh
pacman-key --refresh-keys
```

To reinitialize all keys
```sh
pacman-key --init
pacman-key --populate archlinux
```


### Chroot

Install base packages
```sh
pacstrap -i /mnt base vi
```

Chroot
```sh
arch-chroot /mnt
```

Uncomment the following line `/etc/pacman.conf` using vi editor
```
ParallelDownloads=5
```

Install Packages
```sh
sudo pacman -S --needed base-devel
sudo pacman -S --needed nano neovim
sudo pacman -S --needed linux linux-headers
sudo pacman -S --needed linux-lts linux-lts-headers
sudo pacman -S --needed linux-firmware sof-firmware
sudo pacman -S --needed sudo # Should be in base-devel
sudo pacman -S --needed openssh networkmanager wpa_supplicant wireless_tools netctl
sudo pacman -S --needed ntfs-3g btrfs-progs exfatprogs lvm2
sudo pacman -S --needed dialog
sudo pacman -S --needed intel-ucode
```

Graphics Drivers
```sh
sudo pacman -S --needed mesa
# sudo pacman -S --needed nvidia nvidia-lts
```

Build Linux Image
```sh
mkinitcpio -p linux linux-lts
```


#### Locale

Set locale
Edit `/etc/locale.gen`
Uncomment the following line
```
en_US.UTF-8 UTF-8
```

Generate locale
```sh
locale-gen
```


#### Create users

Set root passwd
```sh
passwd
```

Create user
```sh
useradd -m -G wheel name_of_user

# Can also do
# useradd -m -g users -G wheel name_of_user

```

Set the user's password
```sh
passwd name_of_user
```

Edit /etc/sudoers
```sh
EDITOR=nano visudo
```

Uncomment or add the following line
```
%wheel ALL=(ALL) ALL
```


### GRUB

Installing grub
```sh
sudo pacman -S --needed grub os-prober dosfstools mtools
sudo pacman -S --needed efibootmgr
```


Create `/boot/efi/EFI` directory and mount the FAT32 filesystem in it
```sh
mkdir -p /boot/efi/EFI
```

```sh
# mount /dev/sda1 /boot/efi/EFI
```


Now, for UEFI install

```sh
grub-install --target=x86_64-efi --bootloader-id="archlinux_grub_uefi" --recheck

# Verify using
ls -l /boot/grub
```

Copy
```sh
cp /usr/share/locale/en\@quot/LC_MESSAGES/grub.mo /boot/grub/locale/en.mo
```


Build `/boot/grub/grub.cfg`
```sh
grub-mkconfig -o /boot/grub/grub.cfg
```

Now, exit the chroot environment
```sh
exit
```


### Post install

List timezones
```sh
sudo timedatectl list-timezones
```

Set timezone
```sh
sudo timedatectl set-timezone Asia/Kolkata
```

Set hardware clock running in UTC
```sh
sudo timedatectl set-local-rtc 0
```

Check current timedate settings
```sh
sudo timedatectl status
```

Set hostname
```sh
sudo hostnamectl set-hostname archlinux_hostname
```


Edit `/etc/hosts`
```
127.0.0.1 localhost
127.0.1.1 archlinux_hostname
```



Now, you may install xorg and  window manager or a full blown desktop environment


### Experimental

Reflector
```sh
reflector -c "India" -a 6 --sort rate --save /etc/pacman.d/mirrorlist
```
