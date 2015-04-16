
Update the ports tree by using portsnap

```
portsnap fetch update
```
To automate updating of ports you can take advantage of periodic.

For example, I have, ```/usr/local/etc/periodic/daily/300.portsnap-update```
That simply executes ```portsnap cron update``` daily.
```
#!/bin/sh

/usr/sbin/portsnap cron update

exit 0
```
Afterwards portmaster script can be used to build the ports that have newer versions available

```
cd /usr/ports/ports-mgmt/portmaster
make install clean
```

```
portmaster -a
```
