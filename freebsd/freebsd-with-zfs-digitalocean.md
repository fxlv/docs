# How to install FreeBSD with ZFS filesystem on Digitalocean
### Introduction
Digitalocean provides FreeBSD 10 droplets with UFS filesystem but what if you want to use ZFS?

In this howto you will learn how to install FreeBSD with ZFS on a Digitalocean droplet.

We will use mfsBSD to create a in-memory FreeBSD environment from which we will launch  ```bsdinstall``` system installer.
mfsBSD allows for creating BSD disk images, ISOs and tarballs.
It can also be used to install BSD from within Linux.

### Prerequisites
Digitalocean droplet running FreeBSD 10.1 with at least 2 GB of memory.
FreeBSD will run fine in the smallest droplet but for the install time you have to resize it to have more memory.
In my experience you have to have at least 2 GB of RAM (the $20/month droplet) during installation otherwise you might run out of memory.
Afterwards you can resize it back to the original size.
This is easily doable with [Flexible resizing] (https://www.digitalocean.com/community/tutorials/how-to-resize-your-droplets-on-digitalocean).

By default Digitalocean creates droplets that have ```freebsd``` user preinstalled and this user can use ```sudo``` to gain root.
So I will assume that this is what we use.

## Step 1 -- Preparing for installation

Log in to your new droplet as the ```freebsd``` user and launch bourne shell ```sh```, this will be needed for some of the commands we will run.

```
ssh freebsd@123.123.123.123
# launch bourne shell once you have logged in
sh
```

Then download the necessary files.
```
curl -O http://mfsbsd.vx.sk/release/mfsbsd-2.1.tar.gz
curl -O ftp://ftp.freebsd.org/pub/FreeBSD/releases/amd64/10.1-RELEASE/base.txz
curl -O ftp://ftp.freebsd.org/pub/FreeBSD/releases/amd64/10.1-RELEASE/kernel.txz
```

Extract the mfsBSD itself and change into its directory
```
tar -xvf mfsbsd-2.1.tar.gz
cd mfsbsd-2.1
```
## Step 2 -- Configure network settings
Update mfsBSD config so that once we reboot into it the network would be correctly configured.

```
# copy loader.conf.sample and remove lines containing mfsbsd.autodhcp from it
grep -v mfsbsd.autodhcp conf/loader.conf.sample > conf/loader.conf
# disable using DHCP for network configuration
echo mfsbsd.autodhcp="NO" >> conf/loader.conf
```
set up networking
```
cp conf/rc.conf.sample conf/rc.conf
cat /etc/rc.digitalocean.d/droplet.conf >> conf/rc.conf
cp /etc/resolv.conf conf/resolv.conf
```

## Step 3 -- build mfsBSD package 
Now we are ready to build the FreeBSD tar
```
sudo make tar BASE=$HOME
```
After the make is done, you will have ```mfsbsd-10.1-RELEASE-amd64.tar`` in your current directory.
You need to extract it over root.

```
sudo tar -C / -xvf mfsbsd-10.1-RELEASE-amd64.tar
```
## Step 4 -- Reinstall FreeBSD

Reboot your droplet and open a console from Digitalocean control panel.
After the machine comes up, log in as root (no password).

Then you can launch ```bsdinstall``` and reinstall your droplet with ZFS!


