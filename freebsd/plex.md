Installing is as easy as
```
cd /usr/ports/multimedia/plexmediaserver
make install clean
service plexmediaserver start
```


Enable auto start:
```
sysrc plexmediaserver_enable=YES
```

And go to the web UI to configure:
```
http://localhost:32400/web
```
