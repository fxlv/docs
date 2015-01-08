# DNS resolver on Linux (or rather the glibc version used by gnu/linux)
Every sysadmin has configured the resolver in /etc/resolv.conf. 
But how does the resolver actually works?

## Resolver configuration
Even though seemingly there is not much to configure. 

Typical resolver configuration can look like this
```
search some-domain.com
nameserver 8.8.8.8
nameserver 8.8.4.4
```

But there are actually some additional things that you can set in the config file besides the nameservers and search domain.

First, some defaults (all of this is true for debian linux and probably others as well):

* there can be max 3 nameservers specified (controller by MAXNS in /usr/include/resolv.h)
* max 2 retries are done before switching to the next nameserver
* timeout for a query is 5 seconds
* maximum of 6 domains can be specified in the search directive (with limit of 256 characters in total)
* each nameserver is tried in the order they are specified and the next one is only used if the firts one fails to respond
* search line is the only line where the parser can expect several arguments, on the other lines it is safe to write comments as the parser will simply ignore them
* if the resolv.conf file is absent (or if it is missing or contains invalid nameserer specifications), resolver will use 127.0.0.1 as the DNS servers server.
* the options clause can specify timeout and the behaviour of choosing the listed nameservers

```options rotate timeout:2 attempts:1```

## Case study
There are quite interesting man pages and header files that provide more insight into the inner working of the resolver. For example:
man 5 resolv.conf
man 3 resolver
/usr/include/arpa/nameser.h
/usr/include/resolv.h

Reading all the man pages and checking resolv.h is of course interesting, but me, I like to see how it works in reality.

So just for fun here is what happens if you try to resolve a non-existant hostname and use non-existant DNS servers.

I am using a Debian Wheezy box to test this.
I have 3 non-existant DNS servers specified in the resolv.conf like so:
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
I retried the command several times and every time it was 56 seconds, so this means that the timeouts are the same every single time, which is a bit disappointing. Just to be safe I retried same command on a baremetal server and again same 56 seconds. 
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
#all the libraries get closed and ping gives up
223536 19:09:12.084389716 0 ping (5081) > procexit status=512
```

## The weirdly variating timeouts and logic

So what we can learn from this is that by default each nameserver is tried 4 times in total (2 connection attempts are made and 2 packets are sent each time per connection) and that the default timeouts are 5,3 and 6 seconds ( if 3 nameservers are specified).
This seems a bit peculiar. Quick googling also does not provide any satisfactory explanation. The book "DNS and BIND" describes the timeouts somewhat differently. It says that if you have 3 nameservers then the first timeout is 5 seconds but for next retries it doubles the timeout each time divided by the amount of nameservers. 
So basically it means 5 seconds for first round 10/3 seconds for second, 20/3 seconds for third etc.
But this is definitely not what we are observing here.

If we repeat the tests and override the default timeouts we can see that first timeouts is whatever is set (5 s by default), for next server it is default- ~30% and the last is default +~30%. 
If only two nameservers are specified then the timeout does not get changed and is whatever is specified (5 s by defaut) every time.

Makes no sense to me so source digging it is.

## Digging into the source
After downloading http://ftp.de.debian.org/debian/pool/main/e/eglibc/eglibc_2.13.orig.tar.gz and grepping it for resolver and its timeout logic I found res_send.c which has a function send_dg() that contains following code:
```c
        /*
         * Compute time for the total operation.
         */
        int seconds = (statp->retrans << ns);
        if (ns > 0)
                seconds /= statp->nscount;
        if (seconds <= 0)
                seconds = 1;
```
A bit sad that the there are no comments that would explain the decision behind implementing such logic.
In case you don't understand what happens there see https://gist.github.com/fxlv/6657ba422a9e7afd18ac where I have added comments.

So if there is only 1 nameserver the timeout is not touched and it is whatever you have set or haven't set.

Next, if there are two nameservers then the timeout is adjusted by doing a bitwise shift to the left and then actually gets canceled by the next division.
So essentially for 2 nameservers the end result is the same as for one nameserver, nothing changes. And this is a bit weird logic.

But if you have 3 nameservers defined then it gets more interesting.

The timeout gets bitwise shifted to the left by the nameserver number (second server, so 1) and then gets divided by the count of nameservers.

So lets rewrite this into python (https://gist.github.com/fxlv/22490abba76b60684aae):
```python
#!/usr/bin/env python
import sys
# default dns query timeout is 5 seconds
retrans = 5
# give the user a chance to override it by passing an argument
if (len(sys.argv) == 2):
        retrans = int(sys.argv[1])

# 3 dummy IP addresses
nameservers = ["1.1.1.1", "2.2.2.2", "3.3.3.3"]
# nscount is the count of nameservers
nscount = len(nameservers)
# lets loop over them all and replicate the logic found in res_send.c
for nameserver in nameservers:
    ns = nameservers.index(nameserver)

    seconds = ( retrans << ns )
    if (ns > 0):
        seconds = seconds / nscount
    if (seconds <= 0):
        seconds = 1
    # and for visibility we print it out
    print "Nameserver # {} has timeout {}".format(ns,seconds)
```
This script shows what will be the actual timeout based on the initial timeout setting.
If the initial (as per default) timeout is 5 seconds then the actual timeouts will be these:
```
$ ./dnslogic.py 5
Nameserver # 0 has timeout 5
Nameserver # 1 has timeout 3
Nameserver # 2 has timeout 6
```

Why this weird choice of logic I have no idea...

But what about other operating systems? Surely someone has to obey the rules.

So I did similar tracing on OpenBSD and it behaved exactly as it should.
First timeout is 5 seconds and every next one is double the timeout divided by nameserver count.
See https://gist.github.com/fxlv/33c8cad7c0c51e264e26 for a full trace.

## Conclusion
I am not sure what to say, I was quite surprised.
Glibc is used by so many distros but do we know what other deviations from standards it contains?




