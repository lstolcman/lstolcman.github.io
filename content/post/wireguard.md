---
title: "Wireguard config for private LAN and/or VPN"
date: 2021-06-29T00:38:00+02:00
draft: false
categories: [tech]
tags: [wireguard,vpn]
---

So I wanted to connect from my laptop to my desktop computer, both which were in separate networks
(desktop was in my home network, laptop was on mobile data).
Of course, it turned out to be impossible the easy way due to CGNATs everywhere (actually both networks were CGNATed).

The solution is VPN. After checking the available options, I decided to set up my own.
My thought was - why pay 5 EUR for something, when I can have a VPS for less, and use it
also for other purposes, not only VPN (think e.g. docker).

So the options are good old OpenVPN, or new and shining Wireguard.

I had some experience with OpenVPN, and remembered that the configuration is not so easy.
So I went into [Wireguard quickstart](https://www.wireguard.com/quickstart/),
and what we can see there - all you need to do is three simple steps:

1. configure network interface for wireguard (in linux server)
2. generate keys
3. set wireguard ip in config

Everything is covered nicely in the short documentation.
I did all, and then [generated the keys](https://www.wireguard.com/quickstart/#key-generation) both for server, and my clients.


Example key generation:
```sh
 ⚡ root@vpn  ~/wg_example  wg genkey > priv1 ; cat priv1
cOwSY7He/0/OqmQK/qjCfTpiuDRI8XqPaOM8XoUDWGM=
 ⚡ root@vpn  ~/wg_example  wg pubkey < priv1 > pub1 ; cat pub1
tfDwyv81lxjR5uB6r4NZJVSPM946I1e1ywRIuYP47WE=
 ⚡ root@vpn  ~/wg_example  wg genkey > priv2 ; cat priv2
AK4llkMRIJqhuWQ0fCgpc0ujIwhQlfbMIAcJe96fZEc=
 ⚡ root@vpn  ~/wg_example  wg pubkey < priv2 > pub2 ; cat pub2
QUVp/W3gx2LAQzPpAD5GiOZP21nkh4j+FoectuZFGRM=
```

Lets assume priv1/pub1 is for server, and priv2/pub2 is for (first) client.

I had to play a little bit with config files, because I was not able to connect.
All in all, [this](https://upcloud.com/community/tutorials/get-started-wireguard-vpn/)
tutorial was helpful, so the configuration files are:

- for the server (`/etc/wireguard/wg0.conf`):
```conf
[Interface]
PrivateKey = cOwSY7He/0/OqmQK/qjCfTpiuDRI8XqPaOM8XoUDWGM=
ListenPort = 51820
Address = 10.0.0.1/24
#SaveConfig = true
PostUp = iptables -A FORWARD -i %i -j ACCEPT; iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
PostDown = iptables -D FORWARD -i %i -j ACCEPT; iptables -t nat -D POSTROUTING -o eth0 -j MASQUERADE

# client 1
[Peer]
PublicKey = QUVp/W3gx2LAQzPpAD5GiOZP21nkh4j+FoectuZFGRM=
AllowedIPs = 10.0.0.2/32

# client 2 and following...
#[Peer]
#PublicKey = ...
#AllowedIPs = 10.0.0.3/32

# and so on...
```

`priv1` contents goes to `/etc/wireguard/privatekey`

`pub1` contents goes to `/etc/wireguard/publickey`

- client configuration:

```conf
[Interface]
PrivateKey = sPGfwRtiRASEYf+/9FapA0s6Jurfbfjy8CAfDol8F0E=
Address = 10.0.0.2/32
DNS = 1.1.1.1

[Peer]
PublicKey = VnWSrpqEMirBBhKyhKhQXaCqd/oogfdNYLhKqw2jpHs=
AllowedIPs = 0.0.0.0/0, ::/0
Endpoint = <your wireguard server ip or host>:51820
PersistentKeepalive = 25
```

`AllowedIPs = 0.0.0.0/0, ::/0` means, that all traffic will be routed via VPN (both IPv4 and v6).
If you want to create a virtual LAN, put `AllowedIPs = 10.0.0.0/8` instead - so it will cover
all range of `10.x.x.x` private addresses over your virtual wireguard network.


More on this [in the wireguard documentation](https://www.wireguard.com/#cryptokey-routing).


More on IPv6 forwarding [here](https://docs.pi-hole.net/guides/vpn/wireguard/internal/)
