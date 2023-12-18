---
title: "Fstab" # Title of the blog post.
date: 2023-12-18T08:51:44+05:30 # Date of post creation.
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
  - linux
  - Tag_name2
# comment: false # Disable comment if false.
---


/etc/fstab configuration
<!--more-->

`/etc/fstab` file begins on debian as
```
# /etc/fstab: static file system information.
#
# Use 'blkid' to print the universally unique identifier for a
# device; this may be used with UUID= as a more robust way to name devices
# that works even if disks are added and removed. See fstab(5).
#
# systemd generates mount units based on this file, see systemd.mount(5).
# Please run 'systemctl daemon-reload' after making changes here.
#
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
# / was on /dev/sda2 during installation
# /boot/efi was on /dev/sda1 during installation
```

# Common fstab entries

### fat32/vfat for boot
```
UUID=ABAC-2915  /boot/efi       vfat    umask=0077      0       1
```

### btrfs
```
UUID=142abb53-edaf-dd43-caac-9ba6ca37731e /               btrfs   rw,noatime,space_cache=v2,compress=zstd,ssd,discard=async,subvol=@ 0       0
UUID=142abb53-edaf-dd43-caac-9ba6ca37731e /home           btrfs   rw,noatime,space_cache=v2,compress=zstd,ssd,discard=async,subvol=@home 0       0
UUID=142abb53-edaf-dd43-caac-9ba6ca37731e /var/lib/lxd/storage-pools           btrfs   rw,noatime,space_cache=v2,compress=zstd,ssd,discard=async,subvol=@lxd 0       0
```

Special folders
1. /var/lib/lxd/storage-pools
2. /var/lib/docker/btrfs

Not needed but can be considered
1. /var/lib/libvirt/images



## Reference :-
1. [Archwiki Fstab](https://wiki.archlinux.org/title/fstab)

