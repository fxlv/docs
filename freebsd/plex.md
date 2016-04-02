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

If you want to access your plex server from different subnet then you'll have to add `allowednetworks` to the config.
Edit `/usr/local/plexdata/Plex\ Media\ Server/Preferences.xml` config file and add the following (ofc change subnets to what you use):
```
allowedNetworks="10.234.100.0/255.255.255.0,10.235.100.0/255.255.255.0"
```
Afterwards restart it:
```
service plexmediaserver restart
```

The log file by default is:
```
/usr/local/plexdata/Plex\ Media\ Server/Logs/Plex\ Media\ Server.log
```
