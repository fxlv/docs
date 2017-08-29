# GDB basics
## Running program in debugger 
You can run a program under debugger or you can debug a core.
```
gdb <program>
```
will achieve the former.
Once in the gdb shell type `run` and the program will get executed.

### Break points
You can set break points at specific lines or functions.
For example:
```
break some_file.c:103
break some_other_file:10
break somefunction
```
Afterwards run the program by issuing
```
run
```
and it will be stopped at the first breakpoint.

If you want to debug the program one machine instruction at a time, use "step into"

```
si
```

### Specifying arguments.
In case you want to pass some arguments, use `--args` option like so:

```
gdb --args ./program arg1 arg2 argN
```



## Debugging a core
First of all you need a core to debug. 
If your application crashed then perhaps you already have it, if not, you'll have to help it out a bit.
### Killing it
There are two ways, either kill it in a way that will cause it to dump core or use `gcore`.
Killing could be done with `kill -6` for example, afterwards core will be available in a path dictated by `/proc/sys/kernel/core_pattern`. Also you need to set ulimit to allow dumps. 
```
ulimit -c unlimited
```
### Gcore
This is a much nicer way.
Simply run 
```
gcore <pid>
```
And you'll have your core.

## Using GDB
### Basics

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
### Understanding the environment

If you've set a breakpoint or are just stepping through, it can be useful to see the source code around the point.
This is done with the `list` command or just `l`

To see what variables have been set in the programm being debugged you can do

```
info locals
```

Now, one more useful tip is pretty printing.
Suppose you have some data structure that you want to print:
```
(gdb) print metrics
$3 = {{name = 0x400ae9 "num_open_files", value = 320}, {name = 0x400af8 "num_free_file_handles", value = 0}, {name = 0x400b0e "num_max_open_files", value = 0}}
(gdb)
```
Now this is fine, and this is a simple struct, but what if it was larger, it sure would be nice to print it out in a more human friendly way.
This is where pretty printing comes in.

Enable pretty printing and run the print command again:
```
(gdb) set print pretty
(gdb) print metrics
$4 = {{
    name = 0x400ae9 "num_open_files",
    value = 320
  }, {
    name = 0x400af8 "num_free_file_handles",
    value = 0
  }, {
    name = 0x400b0e "num_max_open_files",
    value = 0
  }}
```
Much nicer.


### Getting a bit more advanced 
If it was a crash (for example sgmentation fault) frame 0 will be the one that helps you understand what happened.

If you have multiple threds, you can get overview about them by doing

```
info threads
```

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
### Logging 
Backtraces can be quite long and it might be a good idea to log the output to a file by doing
```
set logging on
```
this will then log everything to `gdb.txt`

If you want to log to another file then you can do it like this:
```
set logging file someotherfile.log
```
Confirm that all's good by doing
```
show logging
```


### Bonus content
Ther's also a fancy mode that can be invoked by providing `-tui` options like so:
```
gdb -tui ./someprogram core
```

Even fancier still, theres a fantastic dashboard for GDB - https://github.com/cyrus-and/gdb-dashboard
