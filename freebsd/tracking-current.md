svn co https://svn0.eu.freebsd.org/base/head /usr/src

make -j 2 buildworld
make -j 2 buildkernel

make installkernel
reboot 
# once rebooted got back to /usr/src
cd /usr/src
mergemaster -p 
make installworld
