
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

To check if any ports need updating run:
```
portmaster -L
```
to actually upgarde them, run:

```
portmaster -a
```
### Setting build options recursively
Many ports allow to set various build options. 
If a port had many dependencies then it can happen so that the build process gets interrupted many times when each of the dependencies present their build options dialog.
To avoid that you can run 
```
make config-recursive
```
before building the port.

