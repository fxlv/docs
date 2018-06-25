When using my X220 I find that by default the trackpoint is too sensitive.
To tune it more to my liking I decrease sensitivity and speed like so:
```
echo 120 | sudo tee  /sys/devices/platform/i8042/serio1/serio2/sensitivity 
echo 70 | sudo tee  /sys/devices/platform/i8042/serio1/serio2/speed 
```
