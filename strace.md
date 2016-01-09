Some cool ways of using strace

To trace a process and follow all forks:

```
strace -f -p <pid>
```
To profile a process (optionally with all the forks):
```
strace -c -f -p <pid>
```
Or you can run a command directly "under" strace:
```
strace -f ./command
```

Want to have a timestamp - add `-t`or `-tt`for microseconds.

Need to save a log? Add `-o`

For example, trace the `whoami`command and save a nice log with timestamps.

```
trace -tt -o whoami.log whoami
```

Use [Linux syscall table](https://filippo.io/linux-syscall-table/) to find out what all those syscalls do.


Another useful argument is `-s`to override the default sting size that is shown.
Strace will only show first 32 bytes by default, sometimes you want to see more
and that's when `-s`can be useful.
    
