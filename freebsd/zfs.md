To be able to ZFS receive as a regular user, first we need to create a new dataset and set its ownership as well as delegate some rights for the user.

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
