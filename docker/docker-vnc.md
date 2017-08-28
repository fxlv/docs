# Running GUI apps in Docker using VNC

## Quick start

Run your container and expose port `5901` to your host

```
docker run -p 5901:5901 -t -i ubuntu
```

Set up VNC server in the container and run it

```
sudo apt-get install vnc4server
vnc4server -geometry 1400x1000
```

Export `$DISPLAY` environment variable
```
export DISPLAY=< change this to the right DISPLAY name from vnc server output, see example below >
```
example:
```
fx  ~  vnc4server

You will require a password to access your desktops.

Password:
Verify:

New 'dc56ff59ac7e:1 (fx)' desktop is dc56ff59ac7e:1

Creating default startup script /home/fx/.vnc/xstartup
Starting applications specified in /home/fx/.vnc/xstartup
Log file is /home/fx/.vnc/dc56ff59ac7e:1.log
```
Note the DISPLAY name ```dc56ff59ac7e:1```

Run your GUI app

Connect to the VNC server (```127.0.0.1:5901```) from your host OS using one of the [freely available VNC clients](https://help.ubuntu.com/community/VNC/Clients).
For example [TightVNC](http://www.tightvnc.com/).


## Details


Running some sort of half-way decent window manager is a good idea, otherwise, for example the `kdbg` was not being displayed nicely.
So what I'm doing is running `fluxbox` and then launching `kdbg` from the GUI mode. Works great.