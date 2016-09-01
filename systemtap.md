## Installing
```
sudo apt-get install systemtap linux-image-`uname -r`-dbg linux-headers-`uname -r`  
```
Additionally, the `systemtap-doc` package contains manpages for the probes, which is really nice, so you might want to install that as well.

## Oneliners
Listing syscalls:

For example, all `open` syscalls:
```
stap -e 'probe syscall.open { printf("%s(%d)\n", execname(), pid()) }'
```

Or replace `open` with `*` and see everything.
For example:
```
stap -e 'probe syscall.* { if (execname() != "stapio") printf("%s(%d) -> %s\n", execname(), pid(), probefunc()) }'
```


Docs:
https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html-single/SystemTap_Beginners_Guide/
