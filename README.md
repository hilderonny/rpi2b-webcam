# rpi2b-webcam

This repository contains instructions for setting up a simple HTTP server on the Raspberry PI 2B
using a PiCamera module or an USB Webcam. The program captures an image every second and stores it
into the home directory of the pi user. The website includes this image and refreshes every 3 seconds.

## Setup Raspberry PI

First download and flash [Raspberry Pi OS (Legacy) Lite (Debian 11 BullsEye)](https://downloads.raspberrypi.com/raspios_oldstable_lite_armhf/images/raspios_oldstable_lite_armhf-2024-03-12/2024-03-12-raspios-bullseye-armhf-lite.img.xz).
Make sure to use a **Debian 11 BullsEye** release, because only those releases contain the required `libcamera-jpeg` applications.

Use the [Raspberry Pi Imager](https://www.raspberrypi.com/software/) application for flashing and make sure to define an user `pi`, enable SSH and WiFi with an existing WLAN.

## Setup server script

Login to your Raspberry and create a file `/home/pi/start.sh` with the following content.

```sh
#!/bin/bash

# Start this script with sudo!

# Start HTTP server
cd /home/pi
sudo  /usr/bin/python -m http.server 80 &

# Capture images
while :
do
        /usr/bin/libcamera-jpeg --output /home/pi/camera.jpg --nopreview --immediate
        sleep 1
done
```

Make the script runnable with `chmod +x start.sh`.

Make the Raspberry call this script at startup by adding a cron entry:

```
sudo crontab -e
```

Add this line to the crontab.

```
@reboot /home/pi/start.sh
```

## Setup the website

Create a file `/home/pi/index.html` with the following content:

```html
<!DOCTYPE html>
<html>
        <head>
                <meta http-equiv="refresh" content="3">
        </head>
        <body>
                <img src="camera.jpg" height="720"/>
        </body>
</html>
```

## Start the server

Now the server is ready. Reboot it with `sudo reboot` and open a browser with `http://192.168.0.2` replacing the IP address with the address of your Paspberry PI.
