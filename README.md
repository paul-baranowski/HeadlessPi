# HeadlessPi

A couple of weeks ago I decided to pick up a [Raspberry Pi Zero W](http://www.microcenter.com/product/475267/zero_wireless_development_board) from my local MicroCenter. Fast forward to today and I finally found some free time to play around with it, and set it up. 

The Pi Zero has a micro HDMI port instead of the regular one and I unfortunately do not have such a cable. The normal next step would be to go out a buy one but why waste the money if another solution can be found. After searching for a bit I found a way in the form of a headless setup.

Here are the steps I took which will hopefully help you too.

Please note that this will work on any Pi but some ports may be different on the device. I also used OSX to set this up so the commands and programs will work only on a OSX.

The Step summary:
- Step 1 - Download the OS and flash the SD card
- Step 2 - Configure files on the SD card to enable ssh access through a USB cable
- Step 3 - Plug in the Pi / SSH into it / change the default password
- Step 4 - Configure the Pi to access you WLAN
- Step 5 - SSH into the Pi through your WLAN

## Step 1

Download the newest version of [Raspbian](https://www.raspberrypi.org/downloads/raspbian/).

I got the Lite version but I dont think it matters which one you choose. After that finishes go and download [Etcher](https://etcher.io/) which we will use to burn the image onto the microSD card. 

Once installed, pop the microSD card into your machine and launch Etcher.

The setup here is relatively simple:
- Click on Select Image and select the ZIP file (dont unzip).
- If only the microSD card is plugged in this should auto populate, if not select the card manually.
- Hit that Flash button and go watch some Netflix while this completes

https://user-images.githubusercontent.com/5499946/27001437-16589c44-4d98-11e7-9f6f-b473d30eb531.png

## Step 2

After this completes dont eject but open the mounted microSD card (if it does not appear mounted anymore, pull the card out and push it back in).

Once inside there should be a whole series of files, we are interested in two of them:
- **config.txt**
- **cmdline.txt**

**image of folder contents here**

Open **config.txt** in a text editor and add the following line to the bottom of the file:
```
dtoverlay=dwc2
```
and it should look something like this:

**image of config.txt here**

Save the file, close it and open **cmdline.txt** with a text editor. 
Here locate **rootwait** and insert: 
```
modules-load=dwc2,g_ether
```
with exactly once space before and after it, it should look like something like this:

**image of cmdline.txt here**

**Save the file and close it.** 

After both files have been edited, we also need to add a ssh file to the root of the microSD card to enable SSH on the Pi as per [this article](https://www.raspberrypi.org/documentation/remote-access/ssh/). 

Look for **3. Enable SSH on a headless Raspberry Pi** in the link above for moere info.

Best way to do it is by opening up terminal, typing cd and dragging the mounted microSD card into the terminal window. 
```
cd /Volumes/NameOfCard
```
Hit return, then
```
touch ssh
```

## Step 3

We are now ready to fire up the Pi. 

- Eject the microSD card from your computer and insert it into the Pi.
- Plug the Pi into your computer using a microUSB cable and make sure to use the **USB** port and not the **POWER** port
- Wait for it to boot and then go to System Preferences > Network
- You should see a "RNDIS/Ethernet Gadget" with a self assigned ip status (if it says Not Connected, give it a few more seconds)

Once it says self assigned ip status you should be able to SSH into the raspberry pi by using:
```
ssh pi@raspberrypi.local
```
Here is should ask you to trust the device to which you will hit: 
```
Y
```
It will ask you for a password which should be **raspberry**. Once logged in you should see:
```
pi@raspberrypi:~ $
```
Next lets change the default password for this account by typing
```
pi@raspberrypi:~ $ passwd
```
This will prompt you to enter the old then new password. 

Now that is complete we should set up the wifi so you can go trully headless.


## Step 4

There are two ways of adding WiFi config to the Pi, either by going to
```
pi@raspberrypi:~ $ sudo nano /etc/wpa_supplicant/wpa_supplicant.conf
```
and adding this to the bottom of the file:
```
network={
    ssid="wifiname"
    psk="password"
}
```
or you can be a little more secure about it by first doing:
```
pi@raspberrypi:~ $ wpa_passphrase "wifiname" "password"
```
and then it should generate something like this:
```
network={
	ssid="wifiname"
	#psk="password"
	psk=f75456c47184a26e539577203ec597ccb83rfgf75801915a00f3aa1bac85234e
}
```
!Note that you **CAN** delete the **#psk** line as it has your password visible but it is commented out.

Next up you can go back to begining of Step 4 and do:
```
pi@raspberrypi:~ $ sudo nano /etc/wpa_supplicant/wpa_supplicant.conf
```
and then add to the bottom of the file:
```
network={
	ssid="wifiname"
	#psk="password"
	psk=f75456c47184a26e539577203ec597ccb83rfgf75801915a00f3aa1bac85234e
}
```
Hit
```
Control+X
```
and then
```
Y
```
to save and exit the file.

Now the Pi should automatically pick it up and you can check if everything is hooked up by typing
```
ifconfig wlan0
```
**inet6 addr:** should have some value next to it, if it does then you are good. If it does not try running
```
sudo wpa_cli reconfigure
```

[There is a really good set of documentation on raspberrypi's website on hooking up wifi that I followed in my setup](https://www.raspberrypi.org/documentation/configuration/wireless/wireless-cli.md). It has some more options you can use like adding multiple SSID's and Hidden Networks.

Now safely shutdown the Pi by executing
```
sudo shutdown -h
```
Wait a few seconds until the green light on the Pi stops blinking and then unplug it.


## Step 5

Plug the microUSB into the **POWER** on the Pi (it is the one to the right) and plug the other end into some power (I used a cell phone charger). Once it boots up, it should connect to your wireless network automatically.

Now we need to get the IP address of the device. You can do it anyway you want (logging into the router settings for one), but I like to simply use:
```
arp -a
```
This should produce a list of all the connected devices on your network, and hopefully one of them should be the **raspberrypi**

If its on the list, copy the (IP address).

Now all that is left to do is ssh into the Pi:
```
ssh pi@(insert the ip address there)
i.e
ssh pi@192.168.3.457
```
Hit enter, press Y to trust it and enter your password. If all checks out, you are now ssh-ing into your new RaspberryPi.

# Congrats

There are a few things that you can do going forward:
- Setup apache, php, node on the Pi
- SSH remotely into the Pi by adding some Port Fowarding on port 22 into the Pi
- Install AVS (Amazon Voice Service) on the Pi and create your very own Alexa
Im going to try to turn these suggestions into links to more Tutorials in the near future.
