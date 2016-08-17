# Raspberry Pi 3 Setup
The steps I took get my Raspberry Pi 3 up and running.

## Change Password
```
passwd
```

## Enable SSH
Launch the Raspbery Config: 
```
sudo raspi-config
```

Go to `9 Advanced Options` then `A4 SSH` and choose `<Enable>`.
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
hdmi_drive=2
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
[Download](https://launchpad.net/ubuntu/trusty/+package/firefox) the latest `armfh` Firefox `.deb` package from Ubuntu Launchpad. 

Install it from the command line: 
```
sudo dpkg -i firefox_*.deb
```

## Mount NAS
Ensure the CFIS packages are installed: 
```
sudo apt-get install cifs-utils
```

Create a credentials file for the username and password: 
```
touch ~/.smbcredentials
```

Add the following contents, replacing the username and password:
```
username=username
password=password
```

Change the permissions of the credentials file:
```
chmod 600 ~/.smbcredentials
```

Make a directory to mount the NAS to: 
```
sudo mkdir /media/NAS
```

Test you can mount the NAS manually: 
```
sudo mount -t cifs //192.168.0.x/NAS /media/NAS -o user=username
```

You can find out your users UID and GID with these commands:
```
id -u username
id -g username
```

Now update the `/etc/fstab` file to auto mount the NAS as **read-only**: 
```
//192.168.0.x/NAS /media/NAS credentials=/home/username/.smbcredentials,iocharset=utf8,_netdev,x-systemd.automount,sec=ntlm,dir_mode=0500,file_mode=0500,uid=1000,gid=1000
```

Or for full **read and write** add this to the `/etc/fstab` instead: 
```
//192.168.0.x/NAS /media/NAS credentials=/home/username/.smbcredentials,iocharset=utf8,_netdev,x-systemd.automount,sec=ntlm,rw,dir_mode=0777,file_mode=0777,uid=1000,gid=1000
```

_Note: Remember to update the commands above with your own NAS IP address, mount directory, username, UID and GID accordingly._

Now run this to mount the NAS:
```
sudo mount -a
```

Create a handy symlink to the NAS on the desktop: 
```
sudo ln -s /media/NAS ~/Desktop/NAS
```

## Links
- https://www.raspberrypi.org/documentation/configuration/config-txt.md
- http://raspberrypi.stackexchange.com/questions/6682/stopping-rasppi-raspbmc-from-auto-changing-source-on-tv
- https://www.raspberrypi.org/forums/viewtopic.php?t=100811
- https://mtantawy.com/quick-tip-how-to-update-to-latest-kodi-16-jarvis-on-raspberry-pi/
- https://www.raspberrypi.org/forums/viewtopic.php?f=63&t=150438
- http://raspberrypi.stackexchange.com/questions/27179/automatic-mounting-of-nas-drive-fails