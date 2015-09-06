Quick and easy way to build the kernel
```
sudo apt-get -y install build-essential bzip2 curl libncurses5-dev bc kernel-package
```
Download the latest and greatest
```
curl -O https://kernel.org/pub/linux/kernel/v4.x/linux-4.1.6.tar.xz
```

Remove any previous configs and then do a:

Simple eay to only compile that stuff you actually need
```
make localyesconfig
```

Build nice deb packages
```
time make-kpkg -j 8 --initrd --append-to-version=azure1 --revision=azure1 kernel-image kernel-headers
```
