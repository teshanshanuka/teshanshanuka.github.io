---
layout: post
title:  "Cross compiling ROS melodic for aarch64"
date:   2020-07-31 01:01:01 +0530
categories: ros ros-melodic aarch64 cross-compilation nvidia-jetson jetson-nano
---

*I cross-compiled ROS for aarch64 to use it on a Jetson nano/tx2*

---

# Overview

I started with [this old repository][1]{:target="_blank"} which was for ROS indigo for armhf. Many cross compilation 
scripts were readily adaptable for melodic. However some needed a bit of tweaking.

My cross compilation scripts are available [in this repo][2]{:target="_blank"} which is a fork of the previously 
mentioned repo. Following ROS components can be cross compiled with it.

* ros_comm
* common_msgs
* sensor_msgs
* image_transport
* vision_opencv
* tf

<font color="red">IMPORTANT:</font> If you want to cross compile, follow the instructions on the README file in the 
repository. This is not a “how to” guide. This just explains some stuff I have done there.


# Important changes for aarch64

|                    | armhf                   | aarch64               |
|--------------------|-------------------------|-----------------------|
| toolset            | gcc-arm                 | gcc-aarch64           |
| c cross compiler   | arm-linux-gnueabihf-gcc | aarch64-linux-gnu-gcc |
| c++ cross compiler | arm-linux-gnueabihf-g++ | aarch64-linux-gnu-g++ |

# Changes from indigo to melodic

**Indigo** needed *tinyxml* but **melodic** (Ubuntu18) needs [*tinyxml2*][3]{:target="_blank"}

Below new packages were added for melodic

* [gpgme and gpg-error (gpgrt)][4]{:target="_blank"}
* [log4cxx][5]{:target="_blank"}
* [openssl][6]{:target="_blank"}

# Compilation

The toolchains should be installed first alongside with other [needed dependencies][7]{:target="_blank"}.

```sh
sudo apt install gcc-aarch64-linux-gnu g++-aarch64-linux-gnu
```

Then all the build scripts must be run (in the correct order – boost needs python and so on). Use the 
`scripts/build_dependencies.sh`.

Use `scripts/xcompile.sh` to start the compilation. It does the following

1. `catkin init` and config the build workspace
2. Use `rosinstall` to create a `.rosinstall` file containing all the packages needed for the build
3. Populate workspace with needed packages using the `.rosinstall` file
4. Build with `catkin build`
5. Do some post-processing stuff (explained in the ‘Post processing’ section)

# Post processing

Some post processing had to be done in the `xcompile.sh` script since there was some problems running the cross-compiled 
ROS as it was.

The path `<install path>/lib` contains the cross-compiled packages’ shared libraries. But ROS commands could not find 
them. Therefore `LD_LIBRARY_PATH` had to be modified.

```sh
LD_LIBRARY_PATH='"${INSTALL_PREFIX}"'/lib:$LD_LIBRARY_PATH
```

`$INSTALL_PREFIX` is the install path provided at the compilation.

In the build machine, aarch64 libraries were installed into the path `/usr/aarch64-linux-gnu/lib/`. But in the host 
(a Jetson nano) they were in `/usr/lib/aarch64-linux-gnu/` (had a problem with ROS not being able to find 
`libpthread.so`). Had to modify the said path for everything to work

```sh
find $INSTALL_PREFIX -type f -exec sed -i 's|/usr/aarch64-linux-gnu/lib/|/usr/lib/aarch64-linux-gnu/|g' {} \;
```

These are done at the end of `xcompile.sh`

# Problems

gpgme, gpgrt and log4cxx cross compilations were unsuccessful. Since the time for this was limited, relevant library 
files are copied from the target machine to the install directory. The files are available in the misc/ directory.

# Cross compiling packages

I was first searching for cross compiling my own set of packages. Asking about this on the Jetson dev forum lead to an 
interesting discussion: [link][8]{:target="_blank"}.

Later I found out that there already is a package for that provided in rostooling: [link][9]{:target="_blank"}.

[1]: https://github.com/mktk1117/ROS_ARM_CROSSCOMPILE
[2]: https://github.com/teshanshanuka/ROS_crosscompile
[3]: http://www.grinninglizard.com/tinyxml2/
[4]: https://www.gnupg.org/software/gpgme/index.html
[5]: https://logging.apache.org/log4cxx/latest_stable/
[6]: https://www.openssl.org/
[7]: https://github.com/teshanshanuka/ROS_crosscompile/blob/melodic_aarch64/scripts/build_dependencies.sh
[8]: https://forums.developer.nvidia.com/t/cross-compile-my-ros-package-for-nvidia-jetson-tx2/142926/12
[9]: https://github.com/ros-tooling/cross_compile
