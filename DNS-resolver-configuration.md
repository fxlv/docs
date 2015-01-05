## BIND is not everything there is
BIND is not the only DNS server out there. You might want to also consider using [NSD][1] and [Unbound][2].
Same goes for DNS tools, ldnsutils package contains drill which is similar to dig.
And more over, NSD and Unbound both should be performing better than BIND.


[RFC1912][3] provides a good read on most common errors in DNS configuration

## Resolver configuration
Even though seemingly there is not much to configure. You can remember few facts (according to /usr/include/resolv.h):
In linux there can be max 3 nameservers specified
and max 2 retries are done before switching to the next nameserver

domain is an obsolete
maximum of 6 domains can be specified in the search directive (with limit of 256 characters in total)
each nameserver is tried in the order they are specified and the next one is only used if the firts one fails to respond
the default timeout is 5 seconds
search line is the only line where the parser can expect several arguments, on the other lines it is safe to write comments as the parser will simply ignore them

if the resolv.conf file does not exist then your system will try to use DNS server on the local server if there is any

the options clause can specify timeout and the behaviour of choosing the listed nameservers

```options rotate timeout:2 attempts:2```

So just for fun we can test the resolver behaviour.
If we have 3 non-existant dns servers specified in our resolv.conf like so:
```
domain testnet
search testnet
nameserver 10.1.1.1
nameserver 10.2.2.2
nameserver 10.3.3.3
```
Let try pinging non-existant hostname and see what happens.
```
vagrant@vagrant-debian-wheezy:~$ time ping dummyyyyyyyy.com
ping: unknown host dummyyyyyyyy.com

real	0m56.077s
user	0m0.000s
sys	0m0.008s
```
So it takes 56 seconds for the resolver to give up and for the ping command to fail. Quite a long time indeed.
203501 19:08:16.009034247 1 ping (5081) < execve res=0 exe=ping args=dummyyyyyyyy.c... tid=5081(ping) pid=5081(ping) ptid=4753(bash) cwd=/home/vagrant fdlimit=1024 pgft_maj=0 pgft_m
Ping gets executed at  19:08:16, resolv.conf gets read, then the system checks for existance of nsdc and reads nsswitch.conf, /etc/host.conf is checked, then /etc/hosts is checked to see if the hostname is specified there.

and this is 1.29 milliseconds later

```
#The first nameserver is tried 
203761 19:08:16.010330599 1 ping (5081) < connect res=0 tuple=10.0.2.15:34042->10.1.1.1:53
203766 19:08:16.010348487 1 ping (5081) > sendto fd=4(<4u>10.0.2.15:34042->10.1.1.1:53) size=34 tuple=NUL

#next nameserver
208906 19:08:21.017266665 1 ping (5081) < connect res=0 tuple=10.0.2.15:47300->10.2.2.2:53
208911 19:08:21.017293777 1 ping (5081) > sendto fd=5(<4u>10.0.2.15:47300->10.2.2.2:53) size=34 tuple=NULL

#next one
209593 19:08:24.022571691 1 ping (5081) < connect res=0 tuple=10.0.2.15:58912->10.3.3.3:53
209593 19:08:24.022571691 1 ping (5081) < connect res=0 tuple=10.0.2.15:58912->10.3.3.3:53

#Now the resolver tries to send the packet again to the servers:
211690 19:08:30.030116084 1 ping (5081) > sendto fd=4(<4u>10.0.2.15:34042->10.1.1.1:53) size=34 tuple=NULL

213109 19:08:35.035511806 1 ping (5081) > sendto fd=5(<4u>10.0.2.15:47300->10.2.2.2:53) size=34 tuple=NULL

213720 19:08:38.036611836 0 ping (5081) > sendto fd=6(<4u>10.0.2.15:58912->10.3.3.3:53) size=34 tuple=NULL

215259 19:08:44.044719264 0 ping (5081) > close fd=4(<4u>10.0.2.15:34042->10.1.1.1:53)
#Now the retry limit of 2 has been reached and the connections (sockets) to all 3 servers get closed.

#And it all starts again
215268 19:08:44.044826594 0 ping (5081) < connect res=0 tuple=10.0.2.15:44273->10.1.1.1:53
215273 19:08:44.044851469 0 ping (5081) > sendto fd=4(<4u>10.0.2.15:44273->10.1.1.1:53) size=42 tuple=NULL
216783 19:08:49.054921323 0 ping (5081) < connect res=0 tuple=10.0.2.15:53206->10.2.2.2:53
216788 19:08:49.054949274 0 ping (5081) > sendto fd=5(<4u>10.0.2.15:53206->10.2.2.2:53) size=42 tuple=NULL


217902 19:08:52.059323446 0 ping (5081) < connect res=0 tuple=10.0.2.15:32926->10.3.3.3:53
217907 19:08:52.059341894 0 ping (5081) > sendto fd=6(<4u>10.0.2.15:32926->10.3.3.3:53) size=42 tuple=NULL

219504 19:08:58.066120266 0 ping (5081) > sendto fd=4(<4u>10.0.2.15:44273->10.1.1.1:53) size=42 tuple=NULL
220892 19:09:03.071869275 0 ping (5081) > sendto fd=5(<4u>10.0.2.15:53206->10.2.2.2:53) size=42 tuple=NULL
221987 19:09:06.075760447 0 ping (5081) > sendto fd=6(<4u>10.0.2.15:32926->10.3.3.3:53) size=42 tuple=NULL

#and again the timeout is reached and all 3 connections get closed
223499 19:09:12.083922104 0 ping (5081) > close fd=4(<4u>10.0.2.15:44273->10.1.1.1:53)

#all the libraries get closed and ping exits with an error 512 at 
223536 19:09:12.084389716 0 ping (5081) > procexit status=512
```



[1]: https://nlnetlabs.nl/projects/nsd/
[2]: https://unbound.net/
[3]: https://www.ietf.org/rfc/rfc1912.txt
