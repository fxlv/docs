# Linux on X220

## TrackPoint

When using my X220 I find that by default the TrackPoint is too sensitive.
To tune it more to my liking I decrease sensitivity and speed by tuning settings via `/sys` filesystem.

To find the right files in `/sys` you can grep `dmesg` output like so:

```console
dmesg | grep serio
```

In my case the output was:
```console
[fx@darkstar ~]$ dmesg | grep serio
[    1.055280] serio: i8042 KBD port at 0x60,0x64 irq 1
[    1.055283] serio: i8042 AUX port at 0x60,0x64 irq 12
[    1.059372] input: AT Translated Set 2 keyboard as /devices/platform/i8042/serio0/input/input3
[    2.126891] psmouse serio1: synaptics: queried max coordinates: x [..5472], y [..4448]
[    2.206469] psmouse serio1: synaptics: Touchpad model: 1, fw: 8.0, id: 0x1e2b1, caps: 0xd001a3/0x940300/0x120c00/0x0, board id: 1611, fw id: 774180
[    2.206504] psmouse serio1: synaptics: serio: Synaptics pass-through port at isa0060/serio1/input0
[    2.261597] input: SynPS/2 Synaptics TouchPad as /devices/platform/i8042/serio1/input/input5
[    2.989335] psmouse serio2: trackpoint: IBM TrackPoint firmware: 0x0e, buttons: 3/3
[    3.242065] input: TPPS/2 IBM TrackPoint as /devices/platform/i8042/serio1/serio2/input/input7
[ 8324.393532] psmouse serio1: synaptics: queried max coordinates: x [..5472], y [..4448]
[fx@darkstar ~]$

```

Now we know that the trackpoint is configured via `/devices/platform/i8042/serio1/serio2/*`
And we tune it by decreasing sensitivity and speed like so:

```bash
echo 120 | sudo tee  /sys/devices/platform/i8042/serio1/serio2/sensitivity
echo 70 | sudo tee  /sys/devices/platform/i8042/serio1/serio2/speed
```

# Disabling toucpad

I looked at /sys/devices/platform/i8042/serio1 which should be the touchpad, but I did not figure out how to disable it.
Instead I found that this can be done using `synclient` like so:

```console
synclient TouchpadOff=1
```

## Making it stick

Since the files in `/sys` are ephemeral, we can use [Systemd'd tmpfiles](https://www.freedesktop.org/software/systemd/man/tmpfiles.d.html) functionality to ensure that they are re-created on every boot.


Create `/etc/tmpfiles.d/trackpoint.conf` with the following content

```systemd
w /sys/devices/platform/i8042/serio1/serio2/sensitivity - - - - 120
w /sys/devices/platform/i8042/serio1/serio2/speed - - - - 70
```