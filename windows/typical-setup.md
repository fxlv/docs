This is how I set up my windows devices.
Might not be the best way but it's my way.

## Software
### Manual install

* Install x64 jre
* Install [Rapid Environment Editor](http://www.rapidee.com/en/download)
* Install [msys2](https://msys2.github.io/)
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

Basics:
```
cinst -y conemu putty.install git.install 7zip.install Far firefox python2 visualstudiocode
```

Media:
```
cinst -y spotify vlc
```

System utilities:
```
cinst -y ccleaner sysinternals crystaldiskinfo win32diskimager windirstat
```

Tools:
```
cinst -y curl winpcap wireshark nmap terraform keepass.install sourcetree fiddler4
```
Restart the powershell (to update path settings)
```
cinst -y  pip github youtube-dl gimp nodejs npm 
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
ext install Spell
ext install PowerShell
ext install output-colorizer
ext install azurerm-vscode-tools
ext install ansible
ext install Theme-1337
ext install cpptools
ext install vscode-instant-markdown
ext install markdownlint
ext install markdown-toc
ext install markdown-shortcuts
```
Enable [auto save](https://code.visualstudio.com/docs/editor/codebasics#_save-auto-save) and linting for Python.
By putting this into your `User Settings`.
```
{
        "files.autoSave": "onFocusChange",
        "python.linting.pylintEnabled": true,
        "python.linting.flake8Enabled": true,
        "python.linting.pydocstyleEnabled": true
}
```

### Python
Pip and easy_install are already installed as part of standard windows python installation.
Some modules need building and for that you'll need [Visual C++ compiler for Python](http://aka.ms/vcpython27) installed.

```
pip install --upgrade pip
pip install ipython bpython pyreadline wmi subliminal
```
(you need to have pyreadline installed on windows to take advantage of tab-completion)

If you're planning on using `wmi`, you'll also need to install [Pywin32](http://sourceforge.net/projects/pywin32/files/pywin32/Build%20219/pywin32-219.win-amd64-py2.7.exe/download)


