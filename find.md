Some common uses of find command.
Just to have a quick cheat sheet

Find all files that match the name that were modified more than an hour ago

    find . -name "some_file" -mmin +60 -ls

Remove files that were last modified at least 80 x 24h ago

    find . -type f -mtime +80 -exec rm -rvf {} \;

List and delete empty directories

    find . -type d -empty -ls -delete  

Find setuid and setgid files

    find / \( -perm 2000 -o -perm 4000 \) -type f -print 

