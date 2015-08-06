Use sed to search for specific lines in the file
Example, search for string `fx` in the file `auth.log`
`-n` argument tells `sed` to only print out matchine lines.
```
sed -n '/fx/p' auth.log
```
