## Install and configure
Install ezjail
```
pkg install ezjail
```

If you are using ZFS then it makes sense to use it for jails as well.
Enable ZFS in ezjail.config by editing `/usr/local/etc/ezjail.conf`

```
# ZFS options
# Setting this to YES will start to manage the basejail and newjail in ZFS
ezjail_use_zfs="YES"
# Setting this to YES will manage ALL new jails in their own zfs
ezjail_use_zfs_for_jails="YES"
# my main zfs storage pool is 'data'
ezjail_jailzfs="data/jails"

```
enable jails

```
echo "ezjail_enable=\"YES\"" >> /etc/rc.conf
```
## Basejail
After configuring, first thing to do is to install the basejail.
Assuming that you have built your own world and that you have not cleaned /usr/obj
you can use 

```
ezjail-admin update -i -p
```
This will do a ```make installworld``` and will also copy over the ports tree to the basejail.


## Managing jails

### Creating

To create a jail, run 
```
ezjail-admin create jailname 'bge0|192.168.4.2'
```
Replace ```bge0``` with the name of the network interface you wish to use on the host machine.

### Deleting 
To stop a jail and delete it run

```
ezjail-admin delete -fw jailname
```

