This is how I set up my windows devices.
Might not be the best way but it's my way.

## Software
### Manual install

* Install x64 jre
* Install [Rapid Environment Editor](http://www.rapidee.com/en/download)
* Install [msys2](https://msys2.github.io/)
* Install [SourceTree](https://www.sourcetreeapp.com/download)
* Install [Acrylic_WiFi_Home](https://www.acrylicwifi.com/en/wlan-software/wlan-scanner-acrylic-wifi-free/)
* Install [Process Hacker](http://processhacker.sourceforge.net/downloads.php)

### Chocolatey 

Open Powershell Admin console:

```
Set-ExecutionPolicy Unrestricted
```

Install [chocolatey](https://chocolatey.org/) and use it to install everything else.

Then use ```choco install``` or ```cinst``` to install packages.
(in admin shell):

```
cinst -y putty.install git.install 7zip.install Far ccleaner sysinternals crystaldiskinfo vlc firefox python2 spotify visualstudiocode
```
Restart the powershell (to update path settings)
```
cinst -y  pip github windirstat youtube-dl winpcap wireshark gimp win32diskimager nmap nodejs npm
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
### Visual studio code
Visual studio code has some [cool and useful extensions](https://marketplace.visualstudio.com/vscode)
Type Ctrl+P to open "Quick Open" and then run following commands:

```
ext install python
ext install vscode-instant-markdown
ext install Spell
ext install PowerShell
```

### Python
Pip and easy_install are already installed as part of standard windows python installation.

(in admin shell):
```
pip install --upgrade pip
pip install ipython pyreadline wmi
```
(you need to have pyreadline installed on windows to take advantage of tab-completion)

If you're planning on using `wmi`, you'll also need to install [Pywin32](http://sourceforge.net/projects/pywin32/files/pywin32/Build%20219/pywin32-219.win-amd64-py2.7.exe/download)

[Microsoft Visual C++ Compiler for Python 2.7](http://aka.ms/vcpython27) is needed to build some modules.

