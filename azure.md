# Using Azure cross platform tools

Besides the powershell commandlets there are also the [Azure xplat](https://github.com/Azure/azure-xplat-cli) tools that can be used to control your Azure stuff.
## Install
Easyest way to install is by using [npm](https://www.npmjs.com/):
```
sudo npm -g update
```
Afterwards you can run the same command to update to latest and greatest.

## Initial configuration
Next step is to download and import your management certificate:
```
azure account download
```
This will open a browser window and you'll download a `*.publishsettings` file.
Import it:
```
azure account import Downloads/<your-subscription-name>-credentials.publishsettings
```

To show what subscriptions are available to you do a :
```
azure account list
```

To choose which one to use:
```
azure account set <subscription name>
```


## Quickstart
Now, let's quickly deploy a Debian Wheezy VM `testytestytest1` with default user `testuser` and using your SSH key from `$HOME` in region `North Europe`
```
azure vm create testytestytest1 6a83c2d016534a7a917bcd21b6e1c0c9__Debian-8-DAILY-amd64-20160302.0 testuser --ssh --ssh-cert ~/.ssh/id_rsa.pub  --no-ssh-password --location "North Europe"
```
Yes, it is that simple.

Once done, you can get all your VMs like so:
```
azure vm list
```

To delete a VM and remove its storage blob:

```
azure vm delete -b <vm name>
```

## Details
```
To list all available Azure locations:
```
azure vm location list
```

To list available VM sizes, the only way to do this seems to be:
```
azure vm location list --json
```

## ARM
To use Azure Resource Manager set the mode like so:
```
azure config mode arm
```
And then you need to log-in:
```
azure login
```
