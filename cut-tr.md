Using cut command.

Cut is quite cool and is flexible in the way you specify which fields you are interested in.
Example:

```
tail iptables-INPUT.log | sed 's/  / /g' | cut -d " " -f 1-3,10,13-14,18,20-23
```

This command nicely converts:
```
Aug  7 15:06:14 vpn kernel: [26728223.229663] iptables-in: Bad IN IN=eth0 OUT= MAC=04:01:17:1b:34:01:00:24:38:ab:b6:00:08:00 SRC=91.200.14.148 DST=12.123.2.169 LEN=122 TOS=0x00 PREC=0x00 TTL=248 ID=40586 DF PROTO=UDP SPT=44932 DPT=1900 LEN=102
```
into
```
Aug 7 15:06:14 IN=eth0 SRC=91.200.14.148 DST=12.123.2.169 TTL=248 DF PROTO=UDP SPT=44932 DPT=1900
```
