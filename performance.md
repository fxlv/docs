#Cpu

Gather info: cat /proc/cpuinfo

System load:

* cat /proc/loadavg
* vmstat
* mpstat -P ALL
* ps
* top
* htop
* [glances](https://github.com/nicolargo/glances)
* pidstat
* [vtop](https://parall.ax/vtop)
* [dstat](http://dag.wiee.rs/home-made/dstat/)

More on [ps](https://github.com/fxlv/docs/blob/master/ps.md) here.

#Memory

Gather info: 
*  `cat /proc/meminfo`
* `pidstat` gather info about memory usage of specific process for 60 second pertiods 5 times: `pidstat -p <pid> -r 60 5`

Tune swappiness `/proc/sys/vm/swappiness`
show swap status
`swapon -s`
if OOM killer comes it kills based on OOM score, this can be seen also by top

#Storage

Gather info: `/proc/diskstats`
```
iostat
lsof
fuser
sar -d
```

#Network
```
sar -n DEV
```

#Gathering system information
`dmidecode`, `dmidecode -t <n>` where n is a number, see man page
