
Update the ports tree by using portsnap

```
portsnap fetch 
portsnap update
```
Afterwards portmaster script can be used to build the ports that have newer versions available

```
cd /usr/ports/ports-mgmt/portmaster
make install clean
```

```
portmaster -a
```
