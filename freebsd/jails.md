First, let's use ZFS for jails.

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

Provided that you have built your own world and that you have not cleaned /usr/obj

```
ezjail-admin update -i -p
```

