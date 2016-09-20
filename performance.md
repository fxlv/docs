# General observability tools

* cat /proc/loadavg
* vmstat
*  top
* htop
* [collectl](http://collectl.sourceforge.net/)
* [ps](https://github.com/fxlv/docs/blob/master/ps.md)
* [glances](https://github.com/nicolargo/glances)
* pidstat
* [vtop](https://parall.ax/vtop)
* [dstat](http://dag.wiee.rs/home-made/dstat/)
* systat (freebsd)

# Tracing & profiling
* strace & truss & dtruss
* dtrace
* stap
* perf
* ltrace

#Cpu
* `cat /proc/cpuinfo`
* mpstat

#Memory

Gather info: 
*  `cat /proc/meminfo`
* `pidstat` gather info about memory usage of specific process for 60 second pertiods 5 times: `pidstat -p <pid> -r 60 5`

Tune swappiness `/proc/sys/vm/swappiness`
show swap status
`swapon -s`
if OOM killer comes it kills based on OOM score, this can be seen also by top or by looking at `/proc/<pid>/oom_score`

#Storage

Gather info: `/proc/diskstats`
```
iostat
iotop
lsof
fuser
sar -d
```

#Network

All of these can be installed from a Debian package
* [ss](http://www.cyberciti.biz/files/ss.html) 
* ifstat
* iptraf
* iftop
* vnstat
* bwm-ng
* speedometer

# The universal tool - SAR

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
