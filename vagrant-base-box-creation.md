# Vagrant box creation steps

Unless you absolutely trust the provider it probably makes sense to build your own vagrant boxes. It is also an interesting learning experience to unserdtand how vagrant box works.

These are the steps to make a Debian vagrant box:

* install debian in virtualbox (try to keep it very minimal)
* install VirtualBox guest additions
* remove unnecessary packages and do a general cleanup
* add vagrant users SSH pubkey
* zero out the free space
* package it up
* test
* profit

## Installing Debian
Install basic debian image, during install specify root pass: vagrant and add user vagrant with password vagrant.
Call that VM: `debian-wheezy` (or whatever, but I will use this name).
Also use the same name for machine hostname, for domain you can set vagrantup.com. Does not really matter.
## Installing additional packages and enabling SSH
After the installation is complete:
```
apt-get update 
apt-get upgrade 
apt-get install -y ssh 
```

Now it makes sense to continue working over SSH session instead of being logged in via VirtualBox

Enable ssh port forwarding:
```
VBoxManage modifyvm "debian-wheezy" --natpf1 "ssh,tcp,,2222,,22"
```

## Continuing over SSH

SSH into the vm
```
ssh vagrant@localhost -p 2222
```

Now we can install additional packages and do some pre-configuration

```
apt-get install -y build-essential sudo zerofree linux-headers-amd64 ca-certificates
```

insert virtualbox guest additions iso and run the install script from there

```
mount /media/cdrom 
bash /media/cdrom/VBoxLinuxAdditions.run
```

modify sudoers file to contain:

```
cat << EOF >> /etc/sudoers
vagrant ALL=(ALL) NOPASSWD: ALL
EOF
```
and download vagrant public ssh key

```
wget https://raw.githubusercontent.com/mitchellh/vagrant/master/keys/vagrant.pub
mkdir .ssh
mv vagrant.pub .ssh/authorized_keys
chmod 600 .ssh/authorized_keys
chmod 700 .ssh
```

Configure locales
```
echo "LC_ALL="en_US.utf-8""| sudo tee /etc/environment
```
then re-generate the locales
```
sudo dpkg-reconfigure locales
```

## Cleanup
Now that the vbox kernel module is built we no longer need the development packages.

```
apt-get remove --purge linux-headers-amd64 build-essential
apt-get autoremove --purge
```

Clean apt cache

```
apt-get clean
```

stop syslog and remove old logs

```
/etc/init.d/rsyslog stop
find /var/log -type f -delete
```
## Zero out the disk
Now either change runlevel by executing init 1 or reboot into single mode and once there, zero out the free space on the disk. This will allow for much better compression once the box is packaged up.

```
mount -o remount,ro /dev/sda1
zerofree /dev/sda1 
```
After this you are done. Shut down the machine and package it up.

## Packaging up
```
vagrant package --base vdebian-wheezy --output vdebian-wheezy.vanilla.14.12.14.box
```
now you can add it to vagrant
```
vagrant box add --name vdebian-wheezy.vanilla.14.12.14 vdebian-wheezy.vanilla.14.12.14.box
```
Confirm that it has been added and test it out

```
mdkdir test
vagrant init vdebian-wheezy.vanilla.14.12.14
vagrant up
```


