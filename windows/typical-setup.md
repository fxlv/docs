This is how I set up my windows devices.

Install x64 jre

Install Rapid Environment Editor - http://www.rapidee.com/en/download

Install visual studuio code editor - https://code.visualstudio.com/updates

Open Powershell Admin console:
Set-ExecutionPolicy Unrestricted

Then install chocolatey


Then:
choco install -y putty.install git.install 7zip.install
choco install -y Far ccleaner sysinternals
choco install -y python2 pip github
choco install -y windirstat youtube-dl winpcap wireshark gimp 
choco install cmder -pre 


Python
Pip and easy_install are already installed as part of standard windows python installation.
(in admin shell):
pip install --upgrade pip
Pip install ipython


