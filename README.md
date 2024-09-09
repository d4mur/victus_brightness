# Temporary patch for brightness issue on HP Victus 15 running Linux

## Overview

This repository provides a temporary patch for a brightness issue found in some HP Victus 15 laptops equipped with an AMD APU and Nvidia GPU. On these systems, running Linux, the maximum screen brightness is often stuck at a very low level, making the display difficult to use in well-lit environments.

## Patch Details

The provided patch is a workaround for the `amdgpu` kernel module, that adjusts the input signals to the maximum range of 0-255, effectively restoring the screen brightness to its full range.

**Note:** This is a temporary solution intended to mitigate the issue until a permanent fix is released upstream. Use this patch at your own risk.

## Warning

Applying this patch requires a basic understanding of kernel compilation and module management. Please make sure that you are comfortable with these tasks before proceeding, as improper application can affect system stability.

## Instructions
>**Note 1:** The following instructions have only been tested on Slackware Current. If you're using a different distribution, please refer to a specific guide for rebuilding a kernel module on your system.
> ___
>**Note 2:** For Arch Linux users: follow this [guide](https://wiki.archlinux.org/title/Kernel/Arch_build_system) and edit the PKGBUILD file by adding these lines inside the **prepare** function, after the line `local src`:
>
>```bash
>echo "Patching the amdgpu driver to fix the Victus brightness issue"
>wget https://raw.githubusercontent.com/d4mur/victus_brightness/main/max_brightness.patch
>patch -p1 < ./max_brightness.patch
>```
>For more information, check out this [discussion](https://github.com/d4mur/victus_brightness/issues/1)
> ___


To apply the patch, follow these steps:

1. **Download kernel source:**
   
Obtain the source code for the kernel version currently running on your system. You can usually find it through your distribution’s package manager or from the kernel’s official website.


3. **Prepare the Kernel Source:**

```bash
tar xjf /usr/src/linux-source-<version>.tar.bz2
cd linux-source-<version>
```

3. **Apply the Patch:**

Copy the provided patch file to the kernel source directory and apply it:

```bash
cp /path/to/brightness-patch/amdgpu-patch.patch .
patch -p1 < max_brightness.patch
```

4. **Rebuild the amdgpu module:**

If you have a custom .config, copy it to the directory and run:
```bash
make oldconfig
```
For more information, refer to a guide for building the Linux Kernel.

Then run
```bash
make SUBDIRS=drivers/gpu/drm/amd/amdgpu/ modules
```

5. **Replace the existing module:**
   
Locate the existing amdgpu.ko module in your system and back it up:

```bash
sudo cp /lib/modules/$(uname -r)/kernel/drivers/gpu/drm/amd/amdgpu/amdgpu.ko /lib/modules/$(uname -r)/kernel/drivers/gpu/drm/amd/amdgpu/amdgpu.ko.bak
```

Then, replace it with the newly compiled module:

```bash
sudo cp drivers/gpu/drm/amd/amdgpu/amdgpu.ko /lib/modules/$(uname -r)/kernel/drivers/gpu/drm/amd/amdgpu/amdgpu.ko
```

6. **Reboot Your System: Restart your system to load the new module:**

```bash
sudo reboot
```

## Disclaimer

This patch is a temporary workaround and may not work on all systems. It has been tested on a limited number of configurations and is not guaranteed to be free of bugs. Please proceed with caution and ensure you have backed up your data before applying this patch.


