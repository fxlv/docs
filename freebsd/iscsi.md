# Creating ISCSI target 
FreeBSD + ZFS is awesome so why not share that with other servers on the LAN.

ISCSI target functionality is included in FreeBSD by default, you only need to create a config file and enable `ctld` service.

# Quickstart
How to create an ISCSI target based on ZFS zvol without authentication.

Create the zvol. 
```
zfs create -V 100G data/iscsi-hyperv1
```
Now, create the ctld config file `/etc/ctl.conf`

Example config looks like this:
```
portal-group pg0 {
        discovery-auth-group no-authentication
        listen 0.0.0.0
        listen [::]
}

target iqn.2012-06.com.example:hyperv1 {
        auth-group no-authentication
        portal-group pg0

        lun 0 {
                path /dev/zvol/data/iscsi-hyperv1
                size 100G
        }
}
```
Set reasonable permissions for the new config file:
```
chmod 640 /etc/ctl.conf
```

Enable `ctld`
```
sysrc ctld_enable=YES
```
And start it:
```
service ctld start
```
