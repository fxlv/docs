Most used commands:

* `s/regexp/replacement/` - search and replace
* `d` - delete
* `p` - print

Use sed to search for specific lines in the file
Example, search for string `fx` in the file `auth.log`
`-n` argument tells `sed` to only print out matchine lines.
```
sed -n '/fx/p' auth.log
```
you can use OR to combine several search patterns
```
sed -n '/DPT=23 \|DPT=22 /p' iptables-INPUT.log
```

When looking at configs it is useful to skip commented out lines and blank lines
```
sed '/^#\|^$/d' /etc/sysctl.conf
```

Another good use case is printing out specific lines from a file.
For example:
```
sed -n '11,14p' /etc/crontab
```
You can find line numbers by using `nl -b a <filename>`

But wait, it gets better, you can also specify the range by providing start and end pattern, like so:
```
sed -n '/Aug  6/,/Aug 7/p' /var/log/messages
```

