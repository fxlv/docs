First, load bhyve module and install ```sysutils/grub2-bhyve``` package.

```
pkg install grub2-bhyve
kldload vmm
```

Set up bridge and tap network interfaces

```
ifconfig bridge0 create
ifconfig tap0 create
sysctl net.link.tap.up_on_open=1
# re0 is my NIC here
ifconfig bridge0 addm re0 addm tap0
ifconfig bridge0 up
```


Create some filesystems for storing VM data

```
zfs create zroot/bhyve
zfs create zroot/bhyve/isos
zfs create zroot/bhyve/disks
```


Create the virtual disk
```
zfs create -V 20G zroot/bhyve/disks/ubuntu1
```

create map file ```/bhyve/disks/ubuntu1.map``` with the following contents:
```
(hd0) /dev/zvol/zroot/bhyve/disks/ubuntu1
(cd0) /bhyve/isos/ubuntu-14.04.2-server-amd64.iso
```
Run the bootloader first
```
grub-bhyve -r cd0 -m /bhyve/disks/ubuntu1.map -M 2048 ubuntu1
```

Then you can launch the vm
```
bhyve -c 2 -m 2048M -H -P -A \
    -l com1,stdio \
    -s 0:0,hostbridge \
    -s 1:0,lpc -s 2:0,virtio-net,tap0 \
    -s 3,ahci-cd,/bhyve/isos/ubuntu-14.04.2-server-amd64.iso \
    -s 4,virtio-blk,/dev/zvol/zroot/bhyve/disks/ubuntu1 ubuntu1
```
