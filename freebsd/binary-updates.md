If you run one of the stable versions then you ca take advantage of binary updates.

To download and install updates all in one go, run:
```
sudo freebsd-update fetch install
```
### Automatic updates
Furthermore, take a look at `/etc/freebsd-update.conf`

and then set up updating from cron by adding the following line to your `/etc/crontab` (for example set up different `MailTo` value)
```
@daily                                  root    freebsd-update cron
```
