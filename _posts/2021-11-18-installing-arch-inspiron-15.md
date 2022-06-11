---
layout: post
title:  "Installing Arch on Dell Inspiron 15-5000"
date:   2021-11-18 01:01:01 +0530
categories: linux arch archlinux
---

*What I went through to get Arch Linux up and running in my new Dell Inspiron 15-5000*

---

# Installation

1. [Download][1] arch

2. Make a live USB by writing the iso image to the device (Make sure to do `/dev/sdx` not including the number 
   like `/dev/sdx1` )

   `cat path/to/archlinux-version-x86_64.iso > /dev/sdx`

3. Go through the [installation guide][2]
      * Use windows EFI partition if one exists (mount in `/mnt/boot`)
      * In `arch-chroot` or using `pacstrap`, make sure to download package `iwd` so that when you boot into the OS, 
        you can connect to the internet

# Getting started

## Trouble with booting

Now that the OS was installed, I tried to boot into it and [this error][3] appeared. I checked if the UUID was correct 
and it was. So found multiple posts with the same problem and [this one][4] finally helped. Followed the exact steps 
mentioned in the last message of the thread.

## Connecting to the internet

After getting into the OS, now I had just a terminal and no internet despite of having iwd installed. So found 
[this thread][5] and did as instructed.

1. `systemctl enable --now systemd-networkd systemd-resolved iwd`

2. `networkctl status -a`

3. To [enable][6] iwd's DHCP client, add the following to `/etc/iwd/main.conf`
   
   ```sh
   [General]
   EnableNetworkConfiguration=true
   ```

Then rebooted and iwctl was working which I used to then connect to the WiFi

## Desktop environment

Then I needed to add a regular user and add a desktop environment. Followed [this article][7] and I installed KDE 
Plasma which looks great IMO.

## Sound

Everything was now setup and working beautifully, except there was no sound. So I tried solutions in the internet and 
finally had to open a [thread here][8]. Stupid me skipped over a small part of the installation guide and hadn't 
installed the package `sof-firmware`.

## NVIDIA drivers

My laptop has a Nvidia MX330 GPU. So [here's how][9] I went about installing Nvidia drivers for this laptop. Having no 
previous experience with packages on arch, I had to do some digging. So the [package I need to install][10] is in 
AUR (Arch User Repository). Which apparently means you need to clone, build and install yourself. I needed to install 
linux headers with `pacman -S linux-headers` which is a requirement for the driver package

1. Clone the package using the URL given in the package page, cd into the folder

2. Do `makepkg --install`

3. For `cuda` and `cudnn`, simply install them with `pacman -S cuda cudnn`

## Other little details

1. To support mounting NTFS drives, do `pacman -S ntfs-3g`
2. For pip and ipython which I often use, `pacman -S python-pip ipython`

## Some more stuff I had to do later

1. Shutdown was taking too long and showed message 'failed to start wait for network to configure'. I had two nw config 
   services running so had to disable one. [Source][11]

   ```sh
   systemctl status NetworkManager.service      # Was running
   systemctl status systemd-networkd.service    # Was running too
   sudo systemctl stop systemd-networkd.service
   sudo systemctl disable systemd-networkd.service
   ```


Well, that's it! Everything works like a charm. Fast and ~800mb RAM usage when idle.

[1]: https://archlinux.org/download/
[2]: https://wiki.archlinux.org/title/installation_guide
[3]: https://bbs.archlinux.org/viewtopic.php?id=250869
[4]: https://bbs.archlinux.org/viewtopic.php?id=263129
[5]: https://www.reddit.com/r/archlinux/comments/ifpinv/cant_connect_to_wifi/g2p819y/?context=3
[6]: https://www.reddit.com/r/archlinux/comments/hr3ci7/connected_with_iwctl_but_no_internet/
[7]: https://itsfoss.com/install-kde-arch-linux/
[8]: https://bbs.archlinux.org/viewtopic.php?pid=2004016#p2004016
[9]: https://wiki.archlinux.org/title/NVIDIA#Installation
[10]: https://aur.archlinux.org/packages/nvidia-470xx-dkms/
[11]: https://www.reddit.com/r/archlinux/comments/gujq7s/failed_to_start_wait_for_network_to_configure/
