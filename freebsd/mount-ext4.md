```
pkg install fusefs-ext4fuse
kldload fuse.ko
ext4fuse /dev/da0s1 /mnt/backupdisk/
```

