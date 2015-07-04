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

### Import/export

You can easily move jails from host to host by using the import/export functionality of iocage

Lets move one jail ```testserver``` from one server to another.

First, note its IP address and the n export it
```
iocage get ip4_addr testserver
iocage export testserver
```

Once it has been exported it will be available in ```/iocage/images```

Copy the *.tar.xz file to the new server and put it to ```/iocage/images``` directory there (create the directory if it does not yet exist).

Now you can import it by doing 
```
iocage import <UUID>
```
Now set the tag, IP address and hostname
```
iocage set tag=testserver <UUID>
iocage set ip4_addr='bge0|10.1.1.5' testserver
iocage set hostname=testserver testserver
iocage set host_hostname=testserver testserver
```

and that's it.
