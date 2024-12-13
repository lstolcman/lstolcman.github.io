---
title: "IPv6 tunnel in OpenWRT"
date: 2024-12-13T22:44:00+02:00
draft: false
categories: [tech]
tags: [openwrt,ipv6]
---


During the install of new internet provider, I asked for ONT instead of a router.
The reason was, I wanted to have a control of a network stack, and possibly - to install
OpenWRT on my own router.

This happened to be possible (thanks Netia!), and besides that, they provide public ipv4 as well
(dynamic, but stays persistent many days if connected).

The next steps were to experiment - test IPv6 network and possibilities.
Installation of a tunnel was half-straightforward - hurricane electric tunnel is/was not used
(they not allow registering account @protonmail address). In the end, I have to reconsider HE tunnel.

But let's dive into (detailed?) setup.

## Tunnel brokers

If your provider does not support ipv6 yet, you have to connect to other provider via some kind of vpn-like
connection, and they have to give you an ipv6 connection, along with an addressess.

Unfortunately, not many tunnel brokers are available these days (as of writing that post indecember 2024).

The current options for IPv6 tunnelling are:

- Hurricane Electric (origin: US, many PoPs) - [https://tunnelbroker.net/](https://tunnelbroker.net/)
- Securebit (Switzerland) - [https://www.tunnelbroker.ch/](https://www.tunnelbroker.ch/)
- NetAsist (Ukraine) - [https://tb.netassist.ua/](https://tb.netassist.ua/)
- Route64 (community, from EU) - [https://route64.org](https://route64.org)


Going from the top to the bottom:

- Hurricane electric does not allow @protonmail mail address, so I skipped that option for now
- Securebit tunnel does not work - RX data is zero bytes
- NetAssist works file
- Route64 does not have capacity (the error message says "No free subnet for transport network found,
  please try later again.")

The only working tunnel so far is NetAssist one. However, more problems to come - they provide only one point
of presence, which is located in Ukraine. Since geolocation is a big thing in current networks, I might reconsider
creating accout at HE, and select endpoint which fits my geo-location.

Note: you may check https://tunnelbroker.services/ as well

## OpenWRT config

Openwrt configuration of IPv6 tunnel is pretty straightforward, I finished it using both LuCI and editing configs via ssh.

You have to:

1. Prerequisities
    1. reachable, public ipv4
    2. Modern openwrt - 23 or higher

        make sure ipv6 is forwarded in the kernel configs:

        ```sh
        net.ipv6.conf.all.forwarding=1  # should be enabled by default in /etc/sysctl.d/10-default
        ```

2. Install necessary packages

    ```conf
    opkg update
    opkg install 6in4 luci-proto-ipv6
    ```

3. Allow IPv6 traffic in firewall 

    `/etc/config/firewall`:
    ```sh
    config rule
        option name 'Allow-protocol-41-tunnelbroker'
        option src 'wan'
        option proto '41'
        option target 'ACCEPT'
    ```

    Restart router afterwards (so 6in4 packages are available)

    `41` is protocol number, see https://en.wikipedia.org/wiki/List_of_IP_protocol_numbers

4. Configure `6in4` tunnel in interfaces list, or via console
    ```sh
    config interface 'wan6'
        option proto '6in4'
        option peeraddr '<ipv4 gateway from tunnel broker>'
        option ip6addr '<your ipv6 from tunnel broker, ending with ::2>'
        option mtu '1480'  # optional
        list ip6prefix '<ipv6 prefix from tunnel broker>'
    ```


## Reading

Some nice links out there

- https://blog.artooro.com/2024/02/25/build-your-own-ipv6-tunnel-broker-using-linode-and-wireguard/
- https://subnettingpractice.com/ipv6-subnet-calculator.html
- https://www.spiritlhl.net/en/guide/incus/incus_custom.html#attach-free-ipv6-address-segments-to-host-machines/
- https://openwrt.org/docs/guide-user/network/ipv6_ipv4_transitioning
- https://eko.one.pl/?p=openwrt-ipv6
