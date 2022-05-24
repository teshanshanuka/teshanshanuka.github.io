---
layout: post
title:  "Cloning a jetson nano"
date:   2020-07-30 01:01:01 +0530
categories: linux ubuntu-18.04 nvidia-jetson jetson-nano
---

*I needed to get a clone of a Jetson nano in which I have some software installed*

---

# Get the device to recovery mode

First the device needs to be connected in the USB recovery mode. [This forum post][1]{:target="_blank"} says you need 
to jump both reset and recovery pins, but I only needed the reset pin

<img src="/assets/images/2020-07-30-01.png" alt="reset pins"/>

1. Jump the J48 power select pins (Jumped with the green wire)
2. Connect the board to the PC
3. Jump the recovery pins (Marked)
4. Power up
5. Remove the recovery pin jumper

Now the USB device must be available. Check lsusb

<img src="/assets/images/2020-07-30-02.png" alt="lsusb"/>

# Flash it

Jetson SDK manager (I used 1.2.0) can be used to download the files needed to flash

1. Deselect Host machine (Not needed for this task)

   <img src="/assets/images/2020-07-30-03.png" alt="sdkm 1"/>

2. Deselect Jetson SDK components (Not needed for this task)

   <img src="/assets/images/2020-07-30-04.png" alt="sdkm 2"/>

When the download is finished, the files will be available under `~Downloads/nvidia/sdkm_downloads`. In there I have,

* Root file system – `Tegra_Linux_Sample-Root-Filesystem_R32.4.3_aarch64.tbz2`
* Image and tools – `Jetson-210_Linux_R32.4.3_aarch64.tbz2`

They need to be extracted using sudo and apply binaries to the rootfs directory. Do the following

```sh
cd  ~/Downloads/nvidia/sdkm_downloads
sudo tar xpf Jetson-210_Linux_R32.4.3_aarch64.tbz2
cd Linux_for_Tegra/rootfs/
sudo tar xpf ../../Tegra_Linux_Sample-Root-Filesystem_R32.4.3_aarch64.tbz2 
sudo ./apply_binaries.sh
```

Now the flash command can be issued to get a backup image. It goes like this

```sh
sudo ./flash.sh -r -k APP -G <image_name> <board_name> mmcblk0p1
```

The board name can be `jetson-nano-qspi-sd`, `jetson-nano-qspi`, `jetson-tx1`, `jetson-tx2` or `jetson-xavier` depending 
on the board (reference: [quick start guide][2]{:target="_blank"}) (My question on 
[Jetson developer forums][3]{:target="_blank"} when I got the board name wrong).

So for me with a sd card nano, the command is

```sh
sudo ./flash.sh -r -k APP -G backup.img jetson-nano-qspi-sd mmcblk0p1
```

My flashing process stopped midway since the nano board turned off suddenly. Maybe because I used a 2A power supply. 
I'll update this when I get a better one and test it out. Right now can't get the board powered up with the same 
supply (works with USB though)

> The project had to be put on hold and I never got a chance to resume it. So this is it... :-(


[1]: https://forums.developer.nvidia.com/t/how-to-put-nvidia-jetson-nano-in-force-recovery-mode/80400
[2]: https://developer.download.nvidia.com/embedded/L4T/r32-2_Release_v1.0/Nano-TX1/l4t_quick_start_guide.txt?CxwYJ0t4h2Oeal0H7eQv0RhNaR9ZJ_IXwvkyRlyyhBcCZVOVk9L3kUBKkp5qBUGVbmI0UL0-eKBZLjLnhTKn4pJefan2WHXGhq-sGAmYE8CGvBuLozbWRbEqec332ZuM0dfvuyDQwDDhUjK5sruQH4kkzfehLWY_W9djbVFJKLEv
[3]: https://forums.developer.nvidia.com/t/flashing-jetson-nano-error-invalid-target-board/144884
