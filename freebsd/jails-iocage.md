## Managing jails with iocage
If you don't quite like ezjail, try out iocage for FreeBSD jail management.

It is super easy to use and requires no configuration at all.

```
cd /usr/ports/sysutils/iocage
make install clean
```

When running it for the first time, you need to download the base system.
```
iocage fetch
```
If you are running 11.0-CURRENT it will fail to download the base system but that's ok.
You can rectify that by going into ```/iocage/download/11.0-CURRENT``` and downloading necessary files manually:
```
wget -nd -m ftp://ftp.freebsd.org/pub/FreeBSD/snapshots/amd64/11.0-CURRENT/*
```

After that's done, creating a new jail is as easy as:
```
iocage create tag=devbox.somedomain ip4_addr="re0|10.10.10.10"
```
