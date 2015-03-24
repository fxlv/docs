To mount ext4 filesystem on FreeBSD ```fusefs-ext4fuse``` package can be used:
```
pkg install fusefs-ext4fuse
kldload fuse.ko
```
find out the name of the disk by doing

```
camcontrol devlist
```

then mount it

```
ext4fuse /dev/da0s1 /mnt/backupdisk/
```

