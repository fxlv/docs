MikroTik routers allow you to set up L2TP server very easily.
This is great, because iOS devices, macOS and even Windows boxes work as L2TP VPN clients nicely and with no 3rd party software required.

## Steps
In order to set up a MikroTik router as your L2TP server you'll need to:
- create an IP pool 
- create a profile
- create secrets/users (one per device/user)
- enable the L2TP server
- add firewall rules
- arp-proxy (optional)

So without any further ado, let's get going.

## Before you begin
Just to avoid breaking existing setup, check if L2TP server is already enabled

```
/interface l2tp-server server print
```

## IP pool

Start by creating an IP pool that will be used by the VPN clients.
Alternatively, you could re-use already existing pool (such as DHCP pool), but a separate pool might be a good idea also to be able to apply different firewall rules etc.

Create a new IP pool (replace IPs with whatever you use on your LAN) that will be assigned to the VPN users:
```
/ip pool add name=l2tp-pool comment="Pool for L2TP VPN purpose" ranges="10.111.230.60-10.111.230.70"
```

## Profile

Now you can create a new `profile` which will use the IP pool you created in the previous step.
Here it is important to define the `local-address`. I use the main IP of my router.

```
/ppp profile add dns-server=8.8.8.8,1.1.1.1 local-address=10.111.230.1 name=vpn-profile remote-address=vpn-pool use-encryption=yes
```

## Secrets

For each client/user you'll want to add a secret:

```
/ppp secret add name="iPad" password="superSecure" profile=l2tp-profile
```

## L2TP server

Enable the L2TP server and specify an ipsec secret that will be used by all clients:

```
/interface l2tp-server server set default-profile=l2tp-profile enabled=yes ipsec-secret="superSecretStuff" use-ipsec=yes
```

This should be enough for a basic setup.


## Firewall

Depending on how you prefer to do your firewall you'll also need to allow the required ports for L2TP through.
Essentially you need to allow through 3 UDP ports - 500, 1701 and 4500 as well as the ipsec-esp protocol.

I do it like so, but it is also possible to fit it into two rules if you wish:

```
/ip firewall filter add chain=input protocol=udp dst-port=500 action=accept in-interface=ether1-uplink comment="L2TP VPN"
/ip firewall filter add chain=input protocol=udp dst-port=1701 action=accept in-interface=ether1-uplink comment="L2TP VPN"
/ip firewall filter add chain=input protocol=udp dst-port=4500 action=accept in-interface=ether1-uplink comment="L2TP VPN"
/ip firewall filter add chain=input comment="L2TP VPN IPSEC ESP" action=accept in-interface=ether1-uplink protocol=ipsec-esp
```

## ARP proxy

Last thing, if you only want to access the internet (NATted) via this VPN then you're fine, but if you also want to access the assets on the LAN, then you need to enable `arp-proxy` on the LAN interface.
