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
echo "cloned_interfaces=\"${cloned_interfaces} lo1\""
service netif cloneup
service ezjail start
```

## Basejail
### Creating basejail
After configuring, first thing to do is to install the basejail.
Assuming that you have built your own world and that you have not cleaned /usr/obj
you can use 

```
ezjail-admin update -i -p
```
This will do a ```make installworld``` and will also copy over the ports tree to the basejail.

This will also create a ```newjail``` which is a flavour that is always applied to a new jail.
You can customize it with whatever default settings you prefer.

For example set a timezone and dns settings for all new jails

```
cp /etc/localtime /data/jails/newjail/etc/
cp /etc/resolv.conf /data/jails/newjail/etc/
```

### Updating basejail
If you have updated the host system from source then you can update the basejail by runnig
```
ezjail-admin update -i
```
Then for each jail update the config files
```
mergemaster -U -D /usr/jails/jailname
```

To update the basejail ports tree do
```
ezjail-admin update -P
```



## Managing jails

### Creating

To create a jail, run 
```
ezjail-admin create jailname 'bge0|192.168.4.2'
```
Replace ```bge0``` with the name of the network interface you wish to use on the host machine.

Configure SSH to start at jail boot

```
echo "sshd_enable='YES'" > /data/jails/jailname/etc/rc.conf
```
And start the jail
```
ezjail-admin start jailname
```

Note that if you plan on running any services that need access to raw sockets (like named, or even plain ping), then you have to enable that for the jail. This can be done in the individual per jail config file in ```/usr/local/etc/ezjail``` directory. Add the following to the jail config:

```
export jail_JAIL_NAME_HERE_parameters="allow.raw_sockets=1"
```
### Snapshots
You can create snapshots for the jails by doing:

```
ezjail-admin snapshot JAIL_NAME
```
Afterwards you can list the existing snapshots for this jail by doing:
```
zfs list -H -t snapshot|grep JAIL_NAME
```

### Deleting 
To stop a jail and delete it run

```
ezjail-admin delete -fw jailname
```

