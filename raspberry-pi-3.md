# Raspberry Pi 3 Setup
The steps to get my Raspberry Pi 3 up and running.

## Setup Display
Ensure the display works nicely over HDMI: 

````
sudo nano /etc/boot.txt
```

Add the following lines to the bottom of the file: 

```
hdmi_group=1
hdmi_mode=16
hdmi_ignore_cec_init=1
hdmi_ignore_cec=1
gpu_mem=128
``` 

In order to disable HDMI CEC you also need to drop a config file on the recovery partition: 

```
sudo su
mount /dev/mmcblk0p1 /mnt
echo hdmi_ignore_cec_init=1 >> /mnt/config.txt
umount /mnt
exit
```

Now reboot the system: 

```
sudo reboot
````

## Install Kodi
Update repositories and install Kodi packages: 

```
sudo apt-get update
sudo apt-get install kodi
```

Create a Kodi shortcut on the desktop that boots in standalone mode (avoids blackscreen on exit): 

```
touch ~/Desktop/kodi-standalone.desktop
```

Add the follow text to the file: 

```
[Desktop Entry]
Version=1.0
Name=Kodi
GenericName=Media Center
Comment=Manage and view your media
Exec=kodi-standalone
Icon=kodi
Terminal=false
Type=Application
Categories=AudioVideo;Video;Player;TV;
```

## Support FAT32 USB Devices

Install the following packages: 

```
sudo apt-get install exfat-fuse exfat-utils
```

## Install Firefox
Install the following packages: 

```
sudo apt-get install iceweasel
```

## Links
- https://www.raspberrypi.org/documentation/configuration/config-txt.md
- http://raspberrypi.stackexchange.com/questions/6682/stopping-rasppi-raspbmc-from-auto-changing-source-on-tv
- https://www.raspberrypi.org/forums/viewtopic.php?t=100811
- https://mtantawy.com/quick-tip-how-to-update-to-latest-kodi-16-jarvis-on-raspberry-pi/