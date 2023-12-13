---
title: "Grub" # Title of the blog post.
date: 2023-12-13T15:47:47+05:30 # Date of post creation.
description: "Article description." # Description used for search engine.
featured: true # Sets if post is a featured post, making appear on the home page side bar.
draft: true # Sets whether to render this page. Draft of true will not be rendered.
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
  - Linux
# comment: false # Disable comment if false.
---

Configuring grub for a multi OS system
<!--more-->

Main grub configuration file is `/etc/default/grub`

### Default Entry = Last Saved
```
GRUB_SAVEDEFAULT=true
GRUB_DEFAULT=saved
```
> Note :
> 1. Comment out entries such as **`GRUB_DEFAULT=0`** for this to take effect
> 2. This does not work if `/boot` partition is in btrfs or lvm filesystem

### Detect other bootable OSes

Activate os-prober
```
GRUB_DISABLE_OS_PROBER=false
```

### Console

For a TUI,
```
GRUB_TERMINAL=console
```

### Update grub.cfg

On Debian
```sh
sudo update-grub
```

On Arch/Fedora (Even Debian)
```sh
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

### Add custom entry

> Note :
> Place custom grub entries in **/etc/grub.d/** not in /etc/default/grub.d/

To add custom menus and submenus edit the file `/etc/grub.d/40_custom`

### Examples

#### Standard

```
# "Shutdown" menu entry
menuentry "System shutdown" {
	echo "System shutting down..."
	halt
}

# "Restart" menu entry
menuentry "System restart" {
	echo "System rebooting..."
	reboot
}

# "UEFI Firmware Settings" menu entry
if [ ${grub_platform} == "efi" ]; then
	menuentry 'UEFI Firmware Settings' --id 'uefi-firmware' {
		fwsetup
	}
fi
```

#### Chainloading a different EFI paritition

```
if [ "${grub_platform}" == "efi" ]; then
	menuentry "Debian on Different SSD" {
		savedefault
		insmod part_gpt
		insmod fat
		insmod chain
		search --no-floppy --fs-uuid --set=root AB12-CD34
		chainloader /EFI/debian/grubx64.efi
	}
fi
```

#### Windows grub entry

```
menuentry 'Windows Boot Manager (on /dev/nvme0n1p1)' --class windows --class os $menuentry_id_option 'osprober-efi-541A-B5FD' {
	savedefault
	insmod part_gpt
	insmod fat
	search --no-floppy --fs-uuid --set=root 541A-B5FD
	chainloader /EFI/Microsoft/Boot/bootmgfw.efi
}

```


