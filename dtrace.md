List providers

```
dtrace -l
```

Be more specific, list all probes from `syscall` provider
```
dtrace -l -P syscall
```

Trace specific syscalls. For example, trace all `exec` syscalls:
```
dtrace -n 'syscall::exec*: { trace(execname); }'
```

Trace open files

```
dtrace -n 'syscall::open*:entry { printf("%s %s",execname,copyinstr(arg0)); }'
```
