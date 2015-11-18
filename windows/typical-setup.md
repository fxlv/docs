This is how I set up my windows devices.

Install x64 jre

Install [Rapid Environment Editor](http://www.rapidee.com/en/download)

Install [visual studio code editor](https://code.visualstudio.com/updates)

Open Powershell Admin console:
```
Set-ExecutionPolicy Unrestricted
```

### Chocolatey 
Install [chocolatey](https://chocolatey.org/) and use it to install everything else.

Then use ```choco install``` or ```cinst``` to install packages.
(in admin shell):

```
cinst -y putty.install git.install 7zip.install
cinst -y Far ccleaner sysinternals vlc
cinst -y python2 pip github
cinst -y windirstat youtube-dl winpcap wireshark gimp 
cinst cmder -pre 
```

To update all the packages run 

```
cup all
```

### Python
Pip and easy_install are already installed as part of standard windows python installation.

(in admin shell):
```
pip install --upgrade pip
Pip install ipython
```


