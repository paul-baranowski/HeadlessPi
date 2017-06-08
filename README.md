# HeadlessPi

A couple of weeks ago I decided to pick up a [Raspberry Pi Zero W](http://www.microcenter.com/product/475267/zero_wireless_development_board) from my local Microcenter. Fast forward to now and I finally found some time to power on this miracle board. 

As some of you may know, the Pi Zero has a single micro HDMI port and I unfortunately do not have such a cable. The normal next step would be to go out a buy one but why waste the money if another solution can be found. After searching for a bit I found such a solution in the form of a headless setup.

Here are the steps that I took and which will hopefully help you too.


## Step 1

Download the newest version of [Raspbian Jessie](https://www.raspberrypi.org/downloads/raspbian/).
I got the Lite version but I dont think it matters which one you choose. After that finishes go and download [Etcher](https://etcher.io/) which we will use to burn the image onto the microSD card. Once installed, pop the microSD card into your machine and launch Etcher.

The setup here is relatively simple:
- Click on Select Image and select the ZIP file (dont unzip).
- If only the microSD card is plugged in this should auto populate, if not select the card manually.
- Hit that Flash button and go watch some Netflix while this completes

## Step 2

After this completes dont eject but open the mounted microSD card (if it does not appear on anymore, pull the card out and push it back in).

Once inside there should be a whole series of files, we are interested in two of them:
- **config.txt**
- **cmdline.txt**

Open **config.txt** in a text editor and add the following line to the bottom of the file:
```
dtoverlay=dwc2
```
and it should look something like this:

**image here**

Save the file, close it and open **cmdline.txt** with a text editor. 
Here locate **rootwait** and insert: 
```
modules-load=dwc2,g_ether
```
with exactly once space before and after it, it should look like something like this:

**image here**

- Save the file and close it. 

After both files have been edited, we also need to add a ssh file to the root of the microSD card to enable SSH on the Pi as per [this article](https://www.raspberrypi.org/documentation/remote-access/ssh/). 

Look for **3. Enable SSH on a headless Raspberry Pi**

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
