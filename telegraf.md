# Telegraf 
[Telegraf](https://github.com/influxdb/telegraf) is an agent that collects metrics for sending them to InfluxDB
It's written in Go and pretty much works out of the box.
Here's how to get it up and running in few minutes.


## Install:
On a Debian based machine:
```
wget http://get.influxdb.org/telegraf/telegraf_0.2.4_amd64.deb
sudo dpkg -i telegraf_0.2.4_amd64.deb
```

It will now be installed in /opt/telegraf.
Go there and configure it:

./telegraf -sample-config -filter cpu:mem:net:disk:io:netstat -outputfilter influxdb | sudo tee telegraf.conf

Look over the config and update the section that defines how to connect to local influxdb.
If your influxdb host is local then no change is needed (provided you use all the defaults).
If your influxdb host is remote you'll have to specify its hostname/ip and credentials.


Now, test the config:
```
./telegraf -config telegraf.conf -test
```

If the test produced reasonable results, then you can go ahead and copy the config to a place where telegraf expects to find it.
Which, somewhat curiously, is `/etc/opt/telegraf/telegraf.conf`

```
sudo cp -v telegraf.conf /etc/opt/telegraf/telegraf.conf
```

And star the daemon

```
sudo /etc/init.d/telegraf start
```

Check that it runs correctly
```
tail /var/log/telegraf/telegraf.log  -f
```
