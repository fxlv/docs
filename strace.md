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
strace -tt -o whoami.log whoami
```

You can also filter out by types of syscalls and by specific syscalls.

For example, to only show evertyhing network related, do

```
strace -p <pid> -e network
```

Or narrow it down to specific syscalls:
```
strace -p <pid> -e connect,socket
```

For every syscall there's a man page. You can do `man 2 <syscall name>` to find what it does.
Use [Linux syscall table](https://filippo.io/linux-syscall-table/) to find out what all those syscalls do.


Another useful argument is `-s`to override the default string size that is shown.
Strace will only show first 32 bytes by default, sometimes you want to see more
and that's when `-s`can be useful.
    
