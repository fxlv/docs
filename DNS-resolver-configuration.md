# DNS resolver on Linux
Every sysadmin has configured /etc/resolv.conf. 
But does everyone really know how does the resolver works?

## Resolver configuration
Even though seemingly there is not much to configure. 
You can remember few facts (according to /usr/include/resolv.h):
In linux there can be max 3 nameservers specified
and max 2 retries are done before switching to the next nameserver

domain is an obsolete
maximum of 6 domains can be specified in the search directive (with limit of 256 characters in total)
each nameserver is tried in the order they are specified and the next one is only used if the firts one fails to respond
the default timeout is 5 seconds
search line is the only line where the parser can expect several arguments, on the other lines it is safe to write comments as the parser will simply ignore them

if the resolv.conf file is absent (or if it is missing or contains invalid nameserer specifications), resolver will use 127.0.0.1 as the DNS servers server.

the options clause can specify timeout and the behaviour of choosing the listed nameservers

```options rotate timeout:2 attempts:2```

## Case study
There are quite interesting man pages and header files that provide more insight into the inner working of the resolver. For example:
man 5 resolv.conf
man 3 resolver
/usr/include/arpa/nameser.h
/usr/include/resolv.h

Reading all the man pages and checking resolv.h is of course interesting, but me, I like to see how it works in reality.

So just for fun here is what happens if you try to resolve a non-existant hostname and use non-existant DNS servers.

I am using a Debian Wheezy Vagrant box to test this.
I have 3 non-existant dns servers specified in the resolv.conf like so:
```
domain testnet
search testnet
nameserver 10.1.1.1
nameserver 10.2.2.2
nameserver 10.3.3.3
```
And I try pinging non-existant hostname and see what happens.
```
vagrant@vagrant-debian-wheezy:~$ time ping dummyyyyyyyy.com
ping: unknown host dummyyyyyyyy.com

real	0m56.077s
user	0m0.000s
sys	0m0.008s
```
So it takes 56 seconds for the resolver to give up and for the ping command to fail. Quite a long time indeed.
I retried the command several times and everyt time it was 56 seconds, so this means that the timeouts are the same every single time, which is a bit disappointing. Just to be safe I retried same command on a baremetal server and again same 56 seconds. 
But what are they? The manpage of resolv.conf and the resolv.h only defines RES_TIMEOUT to be 5 but that does not add up. If we have 2 retries per nameserver and the timeout is 5 then it should all be over in 30 seconds.

So trace we shall.

### Tracing the resolve operation
Here is the result of the trace I did with some comments

```
203501 19:08:16.009034247 1 ping (5081) < execve res=0 exe=ping args=dummyyyyyyyy.c... tid=5081(ping) pid=5081(ping) ptid=4753(bash) cwd=/home/vagrant fdlimit=1024 pgft_maj=0 pgft_m
```

Ping gets executed at  19:08:16, resolv.conf gets read, then the system checks for existance of nsdc and reads nsswitch.conf, /etc/host.conf is checked, then /etc/hosts is checked to see if the hostname is specified there.

and the actual connections to DNS servers start 1.29 milliseconds later

```
#The first nameserver is tried 
203761 19:08:16.010330599 1 ping (5081) < connect res=0 tuple=10.0.2.15:34042->10.1.1.1:53
# timeout is set to 5 seconds
203766 19:08:16.010348487 1 ping (5081) > sendto fd=4(<4u>10.0.2.15:34042->10.1.1.1:53) size=34 tuple=NUL

# 5 seconds later, the next nameserver is tried, this time timeout is set to 3 seconds
208906 19:08:21.017266665 1 ping (5081) < connect res=0 tuple=10.0.2.15:47300->10.2.2.2:53
208911 19:08:21.017293777 1 ping (5081) > sendto fd=5(<4u>10.0.2.15:47300->10.2.2.2:53) size=34 tuple=NULL

# 3 seconds later the next one, this time timeout is set to 6 seconds
209593 19:08:24.022571691 1 ping (5081) < connect res=0 tuple=10.0.2.15:58912->10.3.3.3:53
209598 19:08:24.022598803 1 ping (5081) > sendto fd=6(<4u>10.0.2.15:58912->10.3.3.3:53) size=34 tuple=NULL

#Now, 6 seconds later the resolver tries to send the packet again to the servers and again uses same timeouts as before: 5,3 and 6 seconds respectively
211690 19:08:30.030116084 1 ping (5081) > sendto fd=4(<4u>10.0.2.15:34042->10.1.1.1:53) size=34 tuple=NULL
213109 19:08:35.035511806 1 ping (5081) > sendto fd=5(<4u>10.0.2.15:47300->10.2.2.2:53) size=34 tuple=NULL
213720 19:08:38.036611836 0 ping (5081) > sendto fd=6(<4u>10.0.2.15:58912->10.3.3.3:53) size=34 tuple=NULL
215259 19:08:44.044719264 0 ping (5081) > close fd=4(<4u>10.0.2.15:34042->10.1.1.1:53)

# now, 28 seconds after the resolving started, the connections (sockets) to all 3 nameservers get closed and re-opened again and the whole process repeats and uses the same timeouts

#And it all starts again
# 5 seconds
215268 19:08:44.044826594 0 ping (5081) < connect res=0 tuple=10.0.2.15:44273->10.1.1.1:53
215273 19:08:44.044851469 0 ping (5081) > sendto fd=4(<4u>10.0.2.15:44273->10.1.1.1:53) size=42 tuple=NULL
# 3 seconds
216783 19:08:49.054921323 0 ping (5081) < connect res=0 tuple=10.0.2.15:53206->10.2.2.2:53
216788 19:08:49.054949274 0 ping (5081) > sendto fd=5(<4u>10.0.2.15:53206->10.2.2.2:53) size=42 tuple=NULL
# 6 seconds
217902 19:08:52.059323446 0 ping (5081) < connect res=0 tuple=10.0.2.15:32926->10.3.3.3:53
217907 19:08:52.059341894 0 ping (5081) > sendto fd=6(<4u>10.0.2.15:32926->10.3.3.3:53) size=42 tuple=NULL
# and retry
219504 19:08:58.066120266 0 ping (5081) > sendto fd=4(<4u>10.0.2.15:44273->10.1.1.1:53) size=42 tuple=NULL
220892 19:09:03.071869275 0 ping (5081) > sendto fd=5(<4u>10.0.2.15:53206->10.2.2.2:53) size=42 tuple=NULL
221987 19:09:06.075760447 0 ping (5081) > sendto fd=6(<4u>10.0.2.15:32926->10.3.3.3:53) size=42 tuple=NULL

#and again the timeout is reached and all 3 connections get closed
223499 19:09:12.083922104 0 ping (5081) > close fd=4(<4u>10.0.2.15:44273->10.1.1.1:53)
```

56 seconds after it all started the resolver gives up
```
#all the libraries get closed and ping exits with an error 512 at 
223536 19:09:12.084389716 0 ping (5081) > procexit status=512
```

## Conclusion
So what we can learn from this is that by default each nameserver is tried 4 times in total (2 connection attempts are made and 2 packets are sent each time per connection) and that the timeouts seemingly are 5,3 and 6 seconds. 
So over all if, for example, your network is down and you have specified 3 nameservers, it will take almost a minute for your machine to give up on resolving DNS.


