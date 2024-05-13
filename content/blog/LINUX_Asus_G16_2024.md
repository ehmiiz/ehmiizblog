--- 
title: "Linux on GU605MI: Sound, Keyboard, Brightness & asusctl"
date: 2024-05-10T08:23:11+01:00 
draft: false

---

{{< figure
  src="/pics/rog_g16.png"
>}}


**Disclaimer: Please note that while these steps have been provided to assist you, I cannot guarantee that they will work flawlessly in every scenario. Always proceed with caution and make sure to back up your data before making any significant changes to your system.**


*Written on 2024-05-08 (Note: Information may become outdated soon, and this was *just* my approach)*

If you're a proud owner of the 2024 Asus Rog Zephyrus G16 (GU605MI) and running Fedora 40+, ensuring smooth functionality of essential features like sound, keyboard, screen brightness, and asusctl might require a bit (hehe) of tweaking.

Here's a comprehensive guide, or really the steps I took, to get everything up and running.

## Ensure Kernel Compatibility

First things first, ensure that your kernel version is at least 6.9.*. If you're on a newer kernel, skip this step.

Kernel 6.9 has audio improvements for Intels new 14th gen CPUs, so it's mandatory for the GU605 to have it.

You might want to research on how to perform this in a safer way.

I trust in Fedora and the Copr build system, so I just executed the following:

```sh
sudo dnf copr enable @kernel-vanilla/mainline
sudo dnf update -y
# Wait for transactions to complete (may take 5+ minutes)
systemctl reboot
```

## Follow the Fedora Workstation Guide

Refer to the Fedora Workstation guide provided by Asus: [Fedora Guide](https://asus-linux.org/guides/fedora-guide/). The steps I took myself where the following:

```sh
# Updates the system
sudo dnf update -y
sudo dnf install https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm

# Installs the nvidia driver
sudo dnf update -y
sudo dnf install kernel-devel
sudo dnf install akmod-nvidia xorg-x11-drv-nvidia-cuda

# Enable hibrenate
sudo systemctl enable nvidia-hibernate.service nvidia-suspend.service nvidia-resume.service nvidia-powerd.service

# Install asusctl and superfxctl, used to interact with the system
# Installs Rog Control gui (to interact with the command line interfaces graphically)
sudo dnf copr enable lukenukem/asus-linux
sudo dnf update

sudo dnf install asusctl supergfxctl
sudo dnf update --refresh
sudo systemctl enable supergfxd.service

sudo dnf install asusctl-rog-gui
```


## Install Firmware, needed as of 2024-05-08

In the future the firmware might be added into the linux-kernel, if the sound works great after you've updated the system, skip this step.

The sound will not work without the correct firmware, we can clone down the correct firmware and copy it over to our system using the following lines:

```sh
git clone https://gitlab.com/kernel-firmware/linux-firmware.git
cd linux-firmware
sudo dnf install rdfind
make install DESTDIR=installdir
sudo cp -r installdir/lib/firmware/cirrus /lib/firmware
systemctl reboot
```

## Fix Screen Brightness

The screens brightness works out of the box while on the dGPU.

However that comes with certain drawbacks, like flickering electron applications and increase in power consumption. The steps below gets the screen brightness controls to work in "Hybrid" and "Integrated" mode (while the display is being ran by the iGPU).

Open the grub configuration file:

```sh
sudo nano /etc/default/grub
```

Add the following string at the end of the line `GRUB_CMD_LINE_LINUX=`:

```sh
quiet splash nvidia-drm.modeset=1 i915.enable_dpcd_backlight=1 nvidia.NVreg_EnableBacklightHandler=0 nvidia.NVreg_RegistryDwords=EnableBrightnessControl=0
```

After editing, the line should look like this:

```sh
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="rd.driver.blacklist=nouveau modprobe.blacklist=nouveau rhgb quiet rd.driver.blacklist=nouveau modprobe.blacklist=nouveau acpi_backlight=native quiet splash nvidia-drm.modeset=1 i915.enable_dpcd_backlight=1 nvidia.NVreg_EnableBacklightHandler=0 nvidia.NVreg_RegistryDwords=EnableBrightnessControl=0"
GRUB_DISABLE_RECOVERY="true"
GRUB_ENABLE_BLSCFG=true
```

Update the grub configuration and reboot:

```sh
sudo grub2-mkconfig -o /boot/efi/EFI/fedora/grub.cfg
systemctl reboot
```

With these steps, I was able get a somewhat functional GU605MI Fedora system. If you encounter any issues, refer to the respective documentation or seek further assistance from the [Asus-Linux](https://asus-linux.org/) community.

### Happy computing!