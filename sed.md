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
