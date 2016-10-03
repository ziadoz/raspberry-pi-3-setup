# Raspberry Pi 3 Setup
The steps I took get my Raspberry Pi 3 up and running.

## Upgrade Jessie
Run the following commands to [upgrade Jessie to Pixel](https://www.raspberrypi.org/blog/introducing-pixel/), which includes Chromium with hardware video support: 
```
sudo apt-get update
sudo apt-get dist-upgrade
sudo apt-get install -y rpi-chromium-mods
sudo apt-get install -y python-sense-emu python3-sense-emu
sudo apt-get install -y python-sense-emu-doc realvnc-vnc-viewer
```

## Change Password
Run the following command, then enter the current password and a new password twice to confirm: 
```
passwd
```

## Setup SSH
Launch the Raspbery Config: 
```
sudo raspi-config
```

Go to `9 Advanced Options` then `A4 SSH` and choose `<Enable>`.

Open the `/etc/ssh/sshd_config` file and add the following: 
```
PermitRootLogin no
ClientAliveInterval 30
ClientAliveCountMax 5
```

Then restart the SSH service:
```
sudo service ssh restart
```

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
nano ~/Desktop/kodi-standalone.desktop
```

Add the following contents to the file: 
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

## Install FAT32 and NTFS File System Support
Install the following packages: 
```
sudo apt-get install exfat-fuse exfat-utils ntfs-3g
```

## Mount NAS
Ensure the CFIS packages are installed: 
```
sudo apt-get install cifs-utils
```

Create a credentials file for the username and password: 
```
touch ~/.smbcredentials
nano ~/.smbcredentials
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

Alternatively you can just use your username and group instead.

Now update the `/etc/fstab` file to auto mount the NAS as **read-only**: 
```
//192.168.0.x/NAS /media/NAS credentials=/home/username/.smbcredentials,iocharset=utf8,_netdev,x-systemd.automount,sec=ntlm,dir_mode=0500,file_mode=0500,uid=username,gid=group 0 0
```

Or for full **read and write** add this to the `/etc/fstab` instead: 
```
//192.168.0.x/NAS /media/NAS credentials=/home/username/.smbcredentials,iocharset=utf8,_netdev,x-systemd.automount,sec=ntlm,rw,dir_mode=0777,file_mode=0777,uid=username,gid=group 0 0
```

_Note: Update the lines above with your own NAS IP address, mount directory, username, UID and GID accordingly._

Now run this to mount the NAS:
```
sudo mount -a
```

Create a handy symlink to the NAS on the desktop: 
```
sudo ln -s /media/NAS ~/Desktop/NAS
```

## Mount USB Drive
Find the UUID of the USB drive: 
```
sudo blkid
ls -l /dev/disk/by-uuid/
```

The output should look something like the following. Note the _UUID_ value:  
```
/dev/sda1: LABEL="USB DRIVE" UUID="1234-5678" TYPE="exfat" PARTUUID="ffffffff-01"
```

Make a directory to mount the USB drive to:
```
sudo mkdir /media/USB-Drive
```

Test mounting the drive manually to make sure it works: 
```
sudo mount -t exfat -o uid=pi,gid=pi UUID=1234-5678 /media/USB-Drive
```

Now add the following to the `/etc/fstab` to auto mount the USB drive: 
```
UUID=1234-5678 /media/USB-Drive exfat defaults,auto,umask=000,users,rw,uid=username,gid=group 0 0
```

_Note: Update the line above with your own UUID, mount directory, file system, UID and GID accordingly._

Now run this to mount the USB drive:
```
sudo mount -a
```

Create a handy symlink to the USB drive on the desktop: 
```
sudo ln -s /media/USB-Drive ~/Desktop/USB-Drive
```

## Install Useful Packages
```
sudo apt-get install tree
```

## Setup Steam Controller
The following instructions should allow you to use the Steam Controller wirelessly.

Download and install the Steam Controller Python drivers: 
```
cd ~/
git clone https://github.com/ynsta/steamcontroller.git
cd steamcontroller
sudo pip install libusb1
sudo python3.4 setup.py install
```

Now create the file rules file:
```
sudo touch /lib/udev/rules.d/99-steam-controller-perms.rules
sudo nano /lib/udev/rules.d/99-steam-controller-perms.rules
```

Then add the following to it: 
```
# This rule is needed for basic functionality of the controller in
# Steam and keyboard/mouse emulation
SUBSYSTEM=="usb", ATTRS{idVendor}=="28de", MODE="0666"

# This rule is necessary for gamepad emulation; make sure you
# replace 'pgriffais' with the username of the user that runs Steam
KERNEL=="uinput", MODE="0660", GROUP="group", OPTIONS+="static_node=uinput"
```

_Note: Replace `group` with the group of user (e.g. `pi`)._

Now reload udev:
```
sudo udevadm control --reload
```

Start the driver and your controller should work: 
```
sc-desktop.py start
```

To start the driver automatically at the desktop create the file:
```
touch ~/.config/autostart/Steam-Controller.desktop
nano ~/.config/autostart/Steam-Controller.desktop
```

Add the following contents to the file: 
```
[Desktop Entry]
Exec=sc-desktop.py start
```

You can also install an on-screen keyboard: 
```
sudo apt-get install matchbox-keyboard
```

And then create a shortcut to it on the desktop:
```
touch ~/Desktop/keyboard.desktop
```

Then add the following contents to the file: 
```
[Desktop Entry]
Name=Keyboard
Exec=matchbox-keyboard
Terminal=false
Type=Application
Icon=matchbox-keyboard
```

## Notes
You can see the icons available for desktop shortcuts in `/usr/share/pixmaps/`

## Links
- https://www.raspberrypi.org/documentation/configuration/config-txt.md
- http://raspberrypi.stackexchange.com/questions/6682/stopping-rasppi-raspbmc-from-auto-changing-source-on-tv
- https://www.raspberrypi.org/forums/viewtopic.php?t=100811
- https://mtantawy.com/quick-tip-how-to-update-to-latest-kodi-16-jarvis-on-raspberry-pi/
- https://www.raspberrypi.org/forums/viewtopic.php?f=63&t=150438
- https://launchpad.net/ubuntu/trusty/+package/firefox
- http://raspberrypi.stackexchange.com/questions/27179/automatic-mounting-of-nas-drive-fails
- http://www.htpcguides.com/properly-mount-usb-storage-raspberry-pi/
- http://www.miqu.me/blog/2015/01/14/tip-exfat-hdd-with-raspberry-pi/
- https://github.com/ynsta/steamcontroller
- http://askubuntu.com/questions/686214/how-do-i-get-a-steam-controller-working
- https://www.raspberrypi.org/forums/viewtopic.php?t=18968
- http://www.techrepublic.com/article/five-tips-for-getting-the-most-out-of-a-raspberry-pi-3-as-a-pc/
- http://raspberrypi.stackexchange.com/questions/44384/how-to-get-chromium-on-raspberry-3/44690
- https://www.raspberrypi.org/blog/introducing-pixel/