# Raspberry Pi 3 Setup
The steps to get my Raspberry Pi 3 up and running.

## Setup Display
Ensure the display works nicely over HDMI: 

````
sudo nano /etc/boot.txt
```

Uncomment or add the following lines as applicable: 

```
disable_overscan=1
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