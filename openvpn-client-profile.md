Here's an example OpenVPN client profile config.

This is the easiest way to import vpn configuration to, say, an iPhone.
You save it somewhere and open it on the iPhone.

I used OneDrive, but I suppose any other storage like dropbox, icloud and so on would work.

```
client
proto tcp
remote YOUR_VPN_SERVER_HOSTNAME
port YOUR_VPN_SERVER_PORT
dev tun
nobind

key-direction 1

<ca>
-----BEGIN CERTIFICATE-----
... your ca cert here ...
-----END CERTIFICATE-----
</ca>

<cert>
-----BEGIN CERTIFICATE-----
... your client cert here ...
-----END CERTIFICATE-----
</cert>

<key>
-----BEGIN PRIVATE KEY-----
... your client key here ...
-----END PRIVATE KEY-----
</key>
```
