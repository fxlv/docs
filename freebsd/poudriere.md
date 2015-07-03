Install poudriere
```
cd /usr/ports/ports-mgmt/poudriere
make install clean
```
Configure in: /usr/local/etc/poudriere.conf

Crate a list of ports to be built. As a starting point you can take all the packages that are installed on your system:
```
portmaster --list-origins | sort -d > /usr/local/etc/poudriere-list
```
Check out a fresh copy of ports
```
poudriere ports -c
```

Create the poudriere jail
```
poudriere jail -c -j current-11_0x64 -v 11.0-CURRENT -a amd64
```

Confirm that all is well:
poudriere jail -l


Sync the ports
```
poudriere ports -u
```
Do the initial build of all ports
```
poudriere bulk -f /usr/local/etc/poudriere-list -j current-11_0x64
```
Later on to update ports and build updated ones:
```
poudriere ports -u
poudriere bulk -f /usr/local/etc/poudriere-list -j current-11_0x64
```


In order to override the default build options for a specific port use:
```
poudriere options -c sysutils/tmux
```
