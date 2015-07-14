## Send and receive
To be able to ZFS receive as a regular user, first we need to create a new dataset and set its ownership as well as delegate some rights for the user.

### On the sending system
Allow everything (```zroot``` in this case) to be sent by ```fx```user
```
zfs allow -u fx send,snapshot zroot
```

### On the receiving system
```
zfs create data/zfs_backup
zfs allow -u fx create,mount,receive data/zfs_backup
chown fx /data/zfs_backup
```
In order for the user to be able to mount the datasets underneath, we have to allow users to mount filesystems
```
sysctl vfs.usermount=1
# make it persistent
echo vfs.usermount=1 >> /etc/sysctl.conf
```
### Sending
First, create a snapshot and then send it
```
zfs send -R zroot/usr/home@test1 | ssh frisbie zfs recv -dvu data/zfs_backup
```

## Tuning and stats
One useful tool is ``sysutils/zfs-stats```
If you run a low-mem system, you might want to lower the maximum amount of memory that ARC can use.
This can be done by setting 
```
# ~200 megs
vfs.zfs.arc_max=209511680
``` 
in your ```/boot/loader.conf```
