# Contact
<a href="mailto:contact@fithome.life">Email Us</a>  
# Overview
We are far from experts when it comes to using the Rasp Pi.  Here we document the goop we do each time we start up a new Rasp Pi.

# Installation
- Put the micro-SD [e.g.: cheap one on Amazon](https://www.amazon.com/gp/product/B004ZIENBA/ref=as_li_ss_tl?ie=UTF8&psc=1&linkCode=sl1&tag=bitknittingwo-20&linkId=923f12067ad3395ed04f043c37d8c39f)  that will hold the Rasp Pi image into an SD Card reader (on our Mac).
- Format using SD-Formatter.
- Download a [Rasp Pi image](https://www.raspberrypi.org/downloads/raspbian/). _Note: We use the Lite image_
- Run Etcher to copy the image onto the SD Card.
- Open a terminal window and cd into the boot drive.  For us this was `cd /Volumes/boot`.
- Add "SSH" file to the root of the image.  We do this by opening a terminal on the boot partition and typing `$touch ssh` 
- Create the `wpa_supplicant.conf` file : `$touch wpa_supplicant.conf`.  Copy the contents into the file `nano wpa_supplicant.conf`:  
```
country=US
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1

network={
    ssid="YOURSSID"
    psk="YOURPWD"
}

```
_Note: Multiple wifi networks can be set up by following [this example](https://www.raspberrypi.org/documentation/configuration/wireless/wireless-cli.md)_:  
  
```
network={
    ssid="SchoolNetworkSSID"
    psk="passwordSchool"
    id_str="school"
}

network={
    ssid="HomeNetworkSSID"
    psk="passwordHome"
    id_str="home"
}
```
 
Changing the ssid and psk to match your network.
- 'safely' remove the SD-card.
- Put the SD-card into the Rasp-Pi's micro-SD port
- Power up the Rasp Pi.  Hopefully wireless is working!

# Headless Config - Connect to Rasp Pi
Put the microSD card into the Rasp Pi and boot it up.  Our next step is to figure out it's IP address.  

## Static IP
Sometimes it is convenient to assign a static IP. We're using Google wifi.  The key to assignment via Google Wifi is use IP addresses that are >= 30 for the fourth byte.
## Figure out IP address
These steps are covered on this [web page](https://itsfoss.com/ssh-into-raspberry/).  

- Figure out the local net our Rasp-Pi is on.
  - Get our local IP address: `ifconfig | grep inet`.  This command gives us:  
    
```
inet 192.168.86.233 netmask 0xffffff00 broadcast 192.168.86.255
inet 192.168.2.1 netmask 0xffffff00 broadcast 192.168.2.255
```
   192.168.2.1 is the [IP address of a router in a home network](https://192-168-1-1ip.mobi/192-168-2-1/).  This means the Rasp-Pi is on 192.168.86.
- Discover Rasp Pi's local IP using Angry IP by scanning 192.168.86.0 to 192.168.86.255.  e.g.: The local IP address on ours was `192.168.86.209`
- ssh e.g.: `ssh pi@192.168.86.209`
- The initial username is `pi` and the initial password is `raspberry`
## Update
Blindly following recommendations,
- `sudo apt-get update`   
- `sudo apt-get upgrade`
- `sudo reboot`

## Also install
We'll also need Git/Github:
- `sudo apt-get install git`  
- `sudo apt-get install python3-venv`
## Python Goop
### Pandas
After installing Pandas/Numpy, we'd get the error:   
```
Original error was: libf77blas.so.3: cannot open shared object file: No such file or directory
```  
This was fixed by running:  
```  
sudo apt-get install libatlas-base-dev
```  
### Bokeh

```
ImportError: libopenjp2.so.7: cannot open shared object file: No such file or directory

sudo apt-get install libopenjp2-7-dev  

ImportError: libtiff.so.5: cannot open shared object file: No such file or directory

sudo apt install libtiff5
```






# Enable Remote VS Code
_NOTE: Remote VS Code [does not support the Rasp Pi Zero (or ZeroW)](https://github.com/microsoft/vscode-remote-release/issues/669)_  
  
YIPPEE! VS Code supports [remote development using SSH](https://code.visualstudio.com/docs/remote/ssh).  We can debug the Python apps running on the Rasp Pi through VS Code.  E-X-C-I-T-I-N-G!

The first thing to do is set up the Rasp Pi for passwordless SSH access as described [in this blog post](https://www.raspberrypi.org/documentation/remote-access/ssh/passwordless.md).

Next go into the VS Code marketplace and install the [Remote Development Extension Pack](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack).

Once that's done, while in VS Code - start up a remote SSH session by going into the commands (through F1) and choose `Remote SSH: Connect to Host`.  And YIPPEE!

# Using SPI
The electricity monitor uses SPI to communicate.  We document the setting up the SPI interface within [the electricity monitor documentation](ElectricityMonitor).





# Other Stuff
Here's some stuff that may or may not be useful.
## Change Time Zone
The Rasp Pi's date/time is set to GMT.  We find it easier to work with dates in our local timezone.  We followed [these instructions](https://www.mbtechworks.com/how-to/change-time-zone-raspbian.html).
## [Mount Drive](#mount_drive)
While it is the most useful to use the remote VS Code goop, there are times when it is nice to see the Rasp Pi drive from the finder.  To do this, we use SSHFS.
- Install [SSHFS](https://osxfuse.github.io/). 
- Create a directory to mount to (e.g.: `/users/auser/mount`).
- Open a terminal window and run (replace the raspPi IP address and mount point) e.g.: `sshfs pi@192.168.86.209: /users/auser/mount`  
  
The `/home/pi` directory of the RaspPi will be mounted as a drive in Finder.
#### Unmount
Sometimes the mount gets into a state of limbo.  When that happens, this command seems to work: `sudo umount -f /users/mj/mount`.
_____________________

## No SSH, Won't connect to wifi
This happened to us (grrrrrrr)... We had files we wanted to access but could not longer connect to the Rasp Pi over ssh....

We got our Rasp Pi in such a tizzy that we couldn't figure the magic incantations to make it all better (a warning to us explorers who blindly trust a blog post about `ufw`).  Luckily we were able to mount the drive on our Mac following [these directions](https://www.jeffgeerling.com/blog/2017/mount-raspberry-pi-sd-card-on-mac-read-only-osxfuse-and-ext4fuse):
```
sudo mkdir /Volumes/rpi
brew cask install osxfuse
brew install ext4fuse
diskutil list
```
Now here is where it gets a tad tricky figuring out what partition ID we want to mount.
```
/dev/disk3 (internal, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:     FDisk_partition_scheme                        *63.9 GB    disk3
   1:             Windows_FAT_32 boot                    268.4 MB   disk3s1
   2:                      Linux                         63.6 GB    disk3s2
```
Our SD Card reader is internal.  We want the Linux partition.  So the ID is `disk3s2`
```
sudo ext4fuse /dev/disk3s2 /Volumes/rpi -o allow_other
```
now we can access the file from Terminal.

At least we can get the files off the SD card!





   
