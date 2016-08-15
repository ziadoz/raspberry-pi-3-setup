# Raspberry Pi 3 Setup
The steps to get my Raspberry Pi 3 up and running.

## Setup Display
Ensure the display works nicely over HDMI: 

````
sudo nano /etc/boot.txt
```

Add the following lines as applicable: 

```
disable_overscan=1
gpu_mem=128
hdmi_ignore_cec_init=1
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