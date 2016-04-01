
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

Another cool trick is to use:
```
portmaster auto
```
Portmaster will figure out what is needed and do it all for you.

### Synth
Nowadays there's another great tool called [Synth](https://github.com/jrmarino/synth).
It allows you to build a local repo of binary packages, it build stuff in parallel and has a nice UI.
Much easier to use than Poudriere.

To upgrade all outdated ports on a system, run:
```
synth upgrade-system
```


### Setting build options recursively
Many ports allow to set various build options. 
If a port had many dependencies then it can happen so that the build process gets interrupted many times when each of the dependencies present their build options dialog.
To avoid that you can run 
```
make config-recursive
```
before building the port.
If you want to download all the dependencies before actual build use:
```
make fetch-recursive
```


