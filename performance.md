#Cpu

Gather info: cat /proc/cpuinfo

System load:

* cat /proc/loadavg
* vmstat
* mpstat -P ALL
* [ps](https://github.com/fxlv/docs/blob/master/ps.md)
* top
* htop
* [glances](https://github.com/nicolargo/glances)
* pidstat
* [vtop](https://parall.ax/vtop)
* [dstat](http://dag.wiee.rs/home-made/dstat/)
* systat (freebsd)
* [ss](http://www.cyberciti.biz/files/ss.html) 


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
ss utility is a great replacement for netstat, it's faster and supports filtering.
Make sure `tcp_diag` module is loaded otherwise it falls back to using /proc filesystem which makes it slower.
Examples.
get overall stats on network sockets

```
ss -s
```

Show all listening sockets
```
ss -l
```

Show  only tcp listening sockets:
```
ss -tl
```

Filter by state
```
ss -ta state FIN-WAIT-1
```


#Gathering system information
`lshw` or `lshw -short` gives a nice overview on the installed hardware
`dmidecode`, `dmidecode -t <n>` where n is a number, see man page
