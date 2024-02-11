---
title: "How to start systemd user services at boot"
date: 2024-02-12T00:10:00+01:00
draft: false
categories: [tech]
tags: [linux,systemd]
---

Covers: ubuntu 22.04

If you separate systemd services with different accounts, you may encounter the problem, that a service is not restarted at boot time, but
once you login into the account. The user (not system-wide) services are ran that way.

To change the behavior (i.e. run the user services at boot time, regardless login), you should not put it in home folder, but in `/etc/systemd/system/`,


Otherwise, you can keep the services in `$HOME` and fake users logging in via:

```sh
sudo loginctl enable-linger <your_user_name>
```

> enable-linger [USER...], disable-linger [USER...]
>     Enable/disable user lingering for one or more users. If
>     enabled for a specific user, a user manager is spawned for
>     the user at boot and kept around after logouts. This allows
>     users who are not logged in to run long-running services.



Note:

`loginctl enable-linger <user>` does permanently enable it even across reboots.
You can verify with `ls /var/lib/systemd/linger/` which lists all users where linger is enabled (a filename in an username).
One of the tasks for loginctl is to create/remove the file.



Note (see ref 2):

To make the service run on boot, you should not put it in your home folder. Instead, put it under /etc/systemd/system/. This is the folder meant to be used by the system administrator (i.e. you) to add new system-wide services.

Other folders include:

    /usr/lib/systemd/system/ is meant for packages that want to install unit files, though under Debian and Ubuntu the folder is actually /lib/systemd/system/ because the various bin and lib folders have not been merged into a unified /usr/ prefix yet.
    /usr/local/lib/systemd/system/ is for installing units by locally compiled packages.


Refs:
1. https://unix.stackexchange.com/questions/585891/start-a-systemd-user-service-at-system-boot-instead-of-at-user-login
2. https://askubuntu.com/questions/676007/how-do-i-make-my-systemd-service-run-via-specific-user-and-start-on-boot
3. https://unix.stackexchange.com/questions/609928/config-file-for-enable-linger-disable-linger