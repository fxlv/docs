#Cpu

Gather info: cat /proc/cpuinfo

System load:

* cat /proc/loadavg
* vmstat
* mpstat -P ALL
* ps
* top
* htop
* [vtop](https://parall.ax/vtop)
* [dstat](http://dag.wiee.rs/home-made/dstat/)

More on [ps](https://github.com/fxlv/docs/blob/master/ps.md) here.

#Memory

Gather info: `cat /proc/meminfo`
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
