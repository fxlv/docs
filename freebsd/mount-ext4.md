To mount ext4 filesystem on FreeBSD ```fusefs-ext4fuse``` package can be used:
```
pkg install fusefs-ext4fuse
```
Now, before you mount anything you need to load the fuse kernel module by doing:

```
kldload fuse.ko
```
You can dd it to /boot/lodaer.conf if you want it to be auto loaded.
```
echo "fuse_load=\”YES\”" >> /boot/loader.conf
```

find out the name of the disk by doing

```
camcontrol devlist
```

then mount it

```
ext4fuse /dev/da0s1 /mnt/backupdisk/
```

