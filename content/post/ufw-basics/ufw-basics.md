---
title: "Basics of UFW - uncomplicated firewall"
date: 2024-02-21T22:14:00+01:00
draft: false
categories: [tech]
tags: [linux,firewall]
---

Configuring ufw for different rules is not difficult at all. However, I will provide couple of commands
which are useful for the home server.


- Enabling ufw: `sudo ufw enable`
- Disabling ufw: `sudo ufw disable`
- Listing available apps: `sudo ufw app list`
- Allowing ssh: `sudo ufw allow OpenSSH` or `sudo ufw allow ssh`
- Allow Syncthing gui, but only to local network:
  - `sudo ufw allow from 192.168.0.0/24 to any app syncthing-gui` - for the ipv4 connection from address 192.168.0.1-254 (local network), to app `syncthing-gui` (app name from the app list)
  - `sudo ufw allow from fe80::/10 to any app syncthing-gui` - for ipv6 connection from fe::/10 ([link-local address](https://en.wikipedia.org/wiki/Link-local_address#IPv6)), to app `syncthing-gui` (app name from the app list).
    The ipv6 address rule is useful because of the `.local` address resolving, which maks to ipv6 address.

Useful digital ocean article about ufw commands: [UFW Essentials: Common Firewall Rules and Commands](https://www.digitalocean.com/community/tutorials/ufw-essentials-common-firewall-rules-and-commands)
