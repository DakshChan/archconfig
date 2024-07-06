# update
```
sudo pacman -Syu
```

# nvidia
```
sudo pacman -S nvidia-dkms nvidia-utils nvidia-settings
```

## set nvidia drm
```
sudo nano sudo nano /boot/loader/entries/*_linux.conf
```
add `nvidia_drm.modeset=1 nvidia_drm.fbdev=1` to options line

## nvidia power management
```
sudo mkdir /etc/modprobe.d/
sudo nano /etc/modprobe.d/nvidia.conf
```
---
```
options nvidia NVreg_DynamicPowerManagement=0x02
```
---

## set nvidia early load
```
sudo mkdir /etc/mkinitcpio.conf.d/
sudo nano /etc/mkinitcpio.conf.d/nvidia.conf
```
---
```
MODULES=(nvidia nvidia_modeset nvidia_uvm nvidia_drm)
```
---
```
sudo mkinitcpio -P
```

# sleep
```
sudo mkdir -p /etc/systemd/sleep.conf.d
sudo nano /etc/systemd/sleep.conf.d/mem-s2idle.conf
```
---
```
[Sleep]
MemorySleepMode=s2idle
```
---

# install yay
```
sudo pacman -S --needed git base-devel
cd /tmp
git clone https://aur.archlinux.org/yay-bin.git
cd yay-bin
makepkg -si
rm -rf /tmp/yay
```

# install isw
```
yay -S isw
sudo nano /etc/isw.conf
```
---
```
# This file is related to isw: https://github.com/YoyPa/isw
#
# [SECTION] contain default cpu and gpu value present at startup.
# You can directly edit it or make your own.
# Laptops are grouped by motherboard name.
#
# Keep address_profile intact, unless you know what you are doing.
#
# Use base16 for address and base10 for temp(°C) or fan_speed(%).
#
# cpu fan will spin at cpu_fan_speed_0:
#               if realtime_cpu_temp is above cpu_temp_0
#                       cpu_fan_speed_1 is applied
#               if realtime_cpu_temp is above cpu_temp_1
#                       cpu_fan_speed_2 is applied
#               [...]
# gpu fan will spin at gpu_fan_speed_0:
#               if realtime_gpu_temp is above gpu_temp_0
#                       gpu_fan_speed_1 is applied
#               if realtime_gpu_temp is above gpu_temp_1
#                       gpu_fan_speed_2 is applied
#               [...]
#
# Some fan_speed value are ignored by laptops, they will trigger the closest valid RPM speed.
# The RPM value displayed by option -r is not exact but close (+-50RPM).
# fan_mode can be 140, 76 or 12, for Advanced, Basic or Auto. Advanced seems to work better with suspend/hibernate.
# cooler_boost seems to be triggered by any value >= 128.
#
# You can find a better explanation of how ec work at:
# https://github.com/YoyPa/isw/wiki/How-EC-work-(for-GS40-6QE-at-least)
#
# You can help me support your laptop (mainly MSI) by openning an issue and providing 'isw -cp MSI_ADDRESS_DEFAULT' output.
# Make sure your dump is made before altering EC with isw, you can reset your EC with a reboot or by changing power source.
#
# [SECTION]
# key = value

[16Q4EMS1]
# GS65_8SE GS65_8SF GS65_8SG GS65_9SD GS65_9SE GS65_9SF GS65_9SG PS65_9SF
# 16Q4EMS1.109
address_profile = MSI_ADDRESS_DEFAULT
fan_mode = 140
# CPU
cpu_temp_0 = 55
cpu_temp_1 = 64
cpu_temp_2 = 70
cpu_temp_3 = 76
cpu_temp_4 = 82
cpu_temp_5 = 88
cpu_fan_speed_0 = 0
cpu_fan_speed_1 = 0
cpu_fan_speed_2 = 30
cpu_fan_speed_3 = 50
cpu_fan_speed_4 = 70
cpu_fan_speed_5 = 100
cpu_fan_speed_6 = 128
# GPU
gpu_temp_0 = 55
gpu_temp_1 = 61
gpu_temp_2 = 65
gpu_temp_3 = 71
gpu_temp_4 = 77
gpu_temp_5 = 86
gpu_fan_speed_0 = 0
gpu_fan_speed_1 = 30
gpu_fan_speed_2 = 50
gpu_fan_speed_3 = 70
gpu_fan_speed_4 = 100
gpu_fan_speed_5 = 128
gpu_fan_speed_6 = 128

[COOLER_BOOST]
address_profile = MSI_ADDRESS_DEFAULT
cooler_boost_off = 0
cooler_boost_on = 128

[USB_BACKLIGHT]
address_profile = MSI_ADDRESS_DEFAULT
usb_backlight_off = 128
usb_backlight_half = 193
usb_backlight_full = 129

[MSI_ADDRESS_DEFAULT]
address_profile = MSI_ADDRESS_DEFAULT
fan_mode_address = 0xf4
cooler_boost_address = 0x98
usb_backlight_address = 0xf7
# CPU
cpu_temp_address_0 = 0x6a
cpu_temp_address_1 = 0x6b
cpu_temp_address_2 = 0x6c
cpu_temp_address_3 = 0x6d
cpu_temp_address_4 = 0x6e
cpu_temp_address_5 = 0x6f
cpu_fan_speed_address_0 = 0x72
cpu_fan_speed_address_1 = 0x73
cpu_fan_speed_address_2 = 0x74
cpu_fan_speed_address_3 = 0x75
cpu_fan_speed_address_4 = 0x76
cpu_fan_speed_address_5 = 0x77
cpu_fan_speed_address_6 = 0x78
realtime_cpu_temp_address = 0x68
realtime_cpu_fan_speed_address = 0x71
realtime_cpu_fan_rpm_address = 0xcc
# GPU
gpu_temp_address_0 = 0x82
gpu_temp_address_1 = 0x83
gpu_temp_address_2 = 0x84
gpu_temp_address_3 = 0x85
gpu_temp_address_4 = 0x86
gpu_temp_address_5 = 0x87
gpu_fan_speed_address_0 = 0x8a
gpu_fan_speed_address_1 = 0x8b
gpu_fan_speed_address_2 = 0x8c
gpu_fan_speed_address_3 = 0x8d
gpu_fan_speed_address_4 = 0x8e
gpu_fan_speed_address_5 = 0x8f
gpu_fan_speed_address_6 = 0x90
realtime_gpu_temp_address = 0x80
realtime_gpu_fan_speed_address = 0x89
realtime_gpu_fan_rpm_address = 0xca
```
---
```
sudo systemctl enable --now isw@16Q4EMS1.service
```

# sddm
```
sudo mkdir /etc/sddm.conf.d/
sudo nano /etc/sddm.conf.d/10-wayland.conf
```
---
```
[General]
DisplayServer=wayland
GreeterEnvironment=QT_WAYLAND_SHELL_INTEGRATION=layer-shell

[Wayland]
CompositorCommand=kwin_wayland --drm --no-lockscreen --no-global-shortcuts --locale1
```
---
