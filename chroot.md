# Creating a chroot

```
CHROOT=/volume1/chroots/debian
mkdir -p $CHROOT
/usr/share/debootstrap/debootstrap stable $CHROOT http://deb.debian.org/debian/
```

Once the debootstrap is done, mount /proc and /sys and copy over some configs and you're ready to chroot.
```
echo "proc $CHROOT/proc proc defaults 0 0" >> /etc/fstab
echo "sysfs $CHROOT/sys sysfs defaults 0 0" >> /etc/fstab

mount proc $CHROOT/proc -t proc
mount sysfs $CHROOT/sys -t sysfs
mount --bind /dev/pts ${CHROOT}/dev/pts

cp /etc/hosts $CHROOT/etc/hosts
cp /proc/mounts $CHROOT/etc/mtab
echo "Entering the chroot"
echo "Welcome to chroot" >> $CHROOT/root/.bashrc
chroot $CHROOT /bin/bash
```

Now that you are in your shiny chroot, let's add some finishing touches.

You might want to set up your locales:

```
echo "en_US.UTF-8 UTF-8" > /etc/locale.gen
locale-gen
```

And work around bug [#685034](https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=685034)
```
cp  /bin/true /usr/bin/ischroot
```
