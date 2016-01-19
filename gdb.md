Some ways of using `gdb` to debug C program cores.

First of all, how to invoke it:
```
gdb ./program core_file
```

For example:
```
gdb ./memleak core.23013
```

First useful thing is backtrace, so once you are in the GDB shell, run `bt`.

For more details use `bt full`

IF the program has threads use `thread apply all bt`

If it was a crash (for example sgmentation fault) frame 0 will be the one that helps you understand what happened.

if you have found a thread that looks interesting, select it and see what happened:

```
thread 1
bt full
```

If you find an interesting frame, you can inspect it closer (if you have the source):
```
frame 0
list +
```


Further useful things to do:
```
maint print statistics
maint print sections
```

