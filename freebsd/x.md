Install Xorg by doing 
```
pkg install xorg
```
Note that HAL and DBUS are no longer needed (at least in FreeBSD 11-CURRENT it is replaced by DEVD).


Configure the X by doing 
```
Xorg -configure
```

This will create a file ```~/xorg.conf.new``` which you can then test by doing 
```
Xorg -config ~/xorg.conf.new -retro
```

If all goes well, move the config to ```/etc/X11/xorg.conf```

Install XFCE4 and add it to ```.xinitrc```.
```
pkg install xfce
echo "startxfce4" > ~/.xinitrc
```
