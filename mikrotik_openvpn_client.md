Before we start, let's define some variables that we shall use:
```
mikrotik=your_mikrotik_ip_or_hostname
vpnserver=vpn_server_ip_or_hostname
tmpdir=/tmp/mikrotikcerts
vpnclient=name_for_your_vpn_client
```
First, generate the certs
Ssh to your VPN server and go itno dorectory that contains the easy-rsa scripts:

Run:
```
cd to/etc/openvpn/easy-rsa
vpnclient=name_for_your_vpn_client
source ./vars
./build-key $vpnclient
```

Now you need to copy the certs to your client, which is the mikrotik, there could be several ways how to do it, I prefer to do it like so (you might want to choose better location that /tmp though):
```
cd keys
tar cvzf /tmp/${vpnclient}_vpn_certs.tar.gz ${vpnclient}.crt ${vpnclient}.key ca.crt
```
Now copy the cert tarball  to your computer:
```
mkdir ${tmpdir}
cd ${tmpdir}
scp ${vpnserver}:/tmp/${vpnclient}_vpn_certs.tar.gz .
```
Unpack them:
```
tar xvzf  ${vpnclient}_vpn_certs.tar.gz 
```

Mikrotik expects private key in pem format, so convert it:
```
openssl rsa -in ${vpnclient}.key -out ${vpnclient}.pem
```

Now copy over the certs to the mikrotik

```
scp ca.crt ${vpnclient}.pem ${vpnclient}.crt admin@${mikrotik}:/
```
Now connect to your  MikroTik and verify that the files are there:
```
ssh admin@${mikrotik}
/file print
```
Import them into MikroTiks certificate store

```
/certificate import file-name=ca.crt
/certificate import file-name=your_vpnclient_name.crt
/certificate import file-name=your_vpnclient_name.pem
```
Verify that all keys are imported:
```
/certificate print
```
It will show two certificates but note the flags, for the private key and cert there should be flags KT, which means both the cert and key are imported.

Now configure openvpnclient on your mikrotik:
```
/interface ovpn-client add name=your_vpn_name_here certificate=the_vpn_cert_name_here mode=ip connect-to=your_vpn_server_address  port=1194 user=nobody
```
Note the username, for some reason mikrotik expects it, but as there is no such thing, just set it to any arbitrary value.

To confirm that VPN is up  do:
```
/ip address print
```
Worth noting: compression is not supported.
Also the CPU on MikroTiks does not support crypto offloading.
