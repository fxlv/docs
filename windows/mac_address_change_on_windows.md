# MAC address changing in Windows 8
Sometimes you find yourself in a situation where the MAC address of your NIC has to be changed.
Especially useful in airports and other WiFi zones.

It is surprisingly simple to do.

Tested on lenovo x220 with intel wifi card.

open regedit and go to :
```
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Class\{4d36e972-e325-11ce-bfc1-08002be10318}
```

there will be many folders : 0000, 0001, 0002, etc. find the one that corresponds to your nic.

They will have some keys:values that will allow you to find your nic.

For example AdapterModel string contains the  nic name.

Create a new key of type "String value" and set  it to your new MAC, for example "A088B49460BC" (just the hex digits with no dividers)

Then disable/enable your nic and you should be all set
