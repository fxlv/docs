## Quick oneliners
(note that this is all about GNU ps)

### Show all processes owned by user `zabbix`
```
ps -u zabbix -FH
```
Example output
```
ID        PID  PPID  C    SZ   RSS PSR STIME TTY          TIME CMD
zabbix   23960     1  0 16771   496   0 Sep05 ?        00:00:00 /usr/sbin/zabbix_agentd
zabbix   23963 23960  0 16771   832   0 Sep05 ?        00:44:27   /usr/sbin/zabbix_agentd: collector [idle 1 sec]
zabbix   23964 23960  0 16771   500   0 Sep05 ?        00:00:00   /usr/sbin/zabbix_agentd: listener #1 [waiting for connection]
zabbix   23965 23960  0 16771   500   0 Sep05 ?        00:00:00   /usr/sbin/zabbix_agentd: listener #2 [waiting for connection]
zabbix   23966 23960  0 16771   500   0 Sep05 ?        00:00:00   /usr/sbin/zabbix_agentd: listener #3 [waiting for connection]
zabbix   23967 23960  0 16771   788   0 Sep05 ?        00:17:52   /usr/sbin/zabbix_agentd: active checks #1 [idle 1 sec]
```

### Show all processes that match the commandline
And sum up CPU time (useful when there are short lived forks), `-C` only works if you know exact commandline name.
```
ps -C sshd  -FH S
```

Example output
```
UID        PID  PPID  C    SZ   RSS PSR STIME TTY      STAT   TIME CMD
root      2946     1  0 12483   868   0 Jun27 ?        Ss    93:09 /usr/sbin/sshd
root     19394  2946  0 20464  3968   0 19:48 ?        Ss     0:00   sshd: fx [priv]
fx       19399 19394  0 20464  1696   0 19:48 ?        S      0:00     sshd: fx@pts/0
root     19583  2946  0 20464  3972   0 20:06 ?        Ss     0:00   sshd: fx [priv]
fx       19588 19583  0 20464  1700   0 20:06 ?        S      0:00     sshd: fx@pts/1
```

### show process tree
```
ps axjf
```
Example output
```
...
    1  2150  2150  2150 ?           -1 Ss     106  60:38 /usr/sbin/nrpe -c /etc/nagios/nrpe.cfg -d
    1  2332  2332  2332 ?           -1 Ss       0   3:38 /usr/lib/postfix/master
 2332  2338  2332  2332 ?           -1 S      105   0:45  \_ qmgr -l -t fifo -u
 2332  9092  2332  2332 ?           -1 S      105   0:00  \_ pickup -l -t fifo -u -c
    1  2398  2397  2397 ?           -1 SL       0 139:26 /usr/sbin/samhain
 2398 15782  2397  2397 ?           -1 S        0   0:04  \_ /usr/sbin/samhain
 ...
```

## simple sorting
### find the top memory user
```
ps aux --sort=-pcpu
```
### find the top memory user
```
ps aux --sort=-rss
```
## customizing the output
### select what stats are printed and combine that with sorting criteria
```
ps axo comm,pid,pcpu,cputime,pmem,rss,vsize,start_time,args  --sort -rss
```

## threads
### show threads
```
ps -eLf
```
or combine that with custom stats selection and sorting
```
ps axo comm,pid,lwp,nlwp,pcpu,cputime,pmem,rss,vsize,start_time,args  -L --sort -rss
```

#### limiting output width 
```
ps axo comm,pid,lwp,nlwp,pcpu,cputime,pmem,rss,vsize,start_time,args  -L --sort -rss --cols 170
```


## other useful things
### Longer usernames in the putput
by default ps only shows usernames if they are <= 8 characters
you can override that like so
```
ps xao user:12,pid,%cpu,%mem,vsz,rss,tty,stat,start,time,comm
```

