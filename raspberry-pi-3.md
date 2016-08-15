# Raspberry Pi 3 Setup
The steps to get my Raspberry Pi 3 up and running.

## Setup Display
Ensure the display works nicely over HDMI: 

````
sudo nano /etc/boot.txt
```

Add the following lines to the bottom of the file: 

```
disable_overscan=1
hdmi_ignore_cec_init=1
gpu_mem=128
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

## Links
- http://raspberrypi.stackexchange.com/questions/6682/stopping-rasppi-raspbmc-from-auto-changing-source-on-tv
- https://www.raspberrypi.org/forums/viewtopic.php?t=100811