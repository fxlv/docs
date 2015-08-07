## Some common uses of find command.
Just to have a quick cheat sheet

### Finding files based on date

Find all files that match the name that were modified more than an hour ago
```
    find . -name "some_file" -mmin +60 -ls
```
Remove files that were last modified at least 80 x 24h ago
```
find . -type f -mtime +80 -exec rm -rvf {} \;
```
Finding files that were created or modified on a specific date or time window
Key here is to use the argument -newerXt where X is one of these: a - access, b - inode creation, c - change, m - modification time.
For example, find all jpg files that were created on May 21st:
```
find . -type f -name "*.jpg" -newermt 2015-05-21 ! -newermt 2015-05-22 -exec ls -lash {} \;
```
### Finding empty directories

List and delete empty directories

    find . -type d -empty -ls -delete  

### Finding setuid/setgid files

Find setuid and setgid files

    find / \( -perm 2000 -o -perm 4000 \) -type f -print 

### Finding files based on size
Find files larger than 100 megabytes and sort the result in human friendly way
```
find /var -type f -size +100M -exec ls -lash {} \;| sort -h
```

