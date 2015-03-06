# simple sorting
### find the top memory user
```
ps aux --sort=-pcpu
```
### find the top memory user
```
ps aux --sort=-rss
```
### select what stats are printed and combine that with sorting criteria
```
ps axo comm,pid,pcpu,cputime,pmem,rss,vsize,start_time,args  --sort -rss
```

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
