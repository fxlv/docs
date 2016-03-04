This is how I set up my windows devices.
Might not be the best way but it's my way.

## Software
### Manual install

* Install x64 jre
* Install [Rapid Environment Editor](http://www.rapidee.com/en/download)
* Install [visual studio code editor](https://code.visualstudio.com/updates) + powershell and python extensions
* Install [msys2](https://msys2.github.io/)

### Chocolatey 

Open Powershell Admin console:

```
Set-ExecutionPolicy Unrestricted
```

Install [chocolatey](https://chocolatey.org/) and use it to install everything else.

Then use ```choco install``` or ```cinst``` to install packages.
(in admin shell):

```
cinst -y putty.install git.install 7zip.install Far ccleaner sysinternals vlc firefox
cinst -y python2 pip github windirstat youtube-dl winpcap wireshark gimp win32diskimager nmap
cinst -y -pre cmder
```

To update all the packages run 

```
cup all
```
it will how you what packages need updating and interactively ask before updating any one of them.

If you feel brawe and don't want to make these hard individual decisions, add `-y` like so:
```
cup -y all
```

### Python
Pip and easy_install are already installed as part of standard windows python installation.

(in admin shell):
```
pip install --upgrade pip
pip install ipython pyreadline wmi
```
(you need to have pyreadline installed on windows to take advantage of tab-completion)

[Pywin32](http://sourceforge.net/projects/pywin32/files/pywin32/Build%20219/pywin32-219.win-amd64-py2.7.exe/download), since you are on windows you might want to install it.

