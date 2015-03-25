Check out the sources
See https://www.freebsd.org/doc/handbook/svn.html for other SVN URLs

```
svn co https://svn0.eu.freebsd.org/base/head /usr/src
```

Before you build you need to have two config files - /etc/src.conf and /etc/make.conf
There's an example make.conf in /usr/share/examples/etc/make.conf which you can take as the basis.

For src.conf please see the man page for the available options, example src.conf on my microserver looks liek this:
```
WITHOUT_ATM=yes
WITHOUT_WIRELESS=yes
WITHOUT_BLUETOOTH=yes
WITHOUT_CALENDAR=yes
WITHOUT_GAMES=yes
```

clear previously built files
```
chflags -R noschg /usr/obj/*
rm -rf /usr/obj
```
build stuff

```
cd /usr/src
time make -j 2 buildworld
time make -j 2 buildkernel
```

```
make installkernel
```
Reboot, and once back up go to /usr/src and there do

```
mergemaster -p 
make installworld
mergemaster -iUF
yes | make delete-old
```

now reboot and when the machine comes back up delete the old libraries
```
yes | make delete-old-libs
```

And you are done.
