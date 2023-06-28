---
title: "Making podman compose actually work in Ubuntu 22.04.2 (WSL2)"
date: 2023-06-28T17:26:00+02:00
draft: false
categories: [tech]
tags: [ubuntu,podman,containers,wsl2]
---

Having a task to do some pet projects, I decided to containerize it, but run it in wsl2 using [podman compose](https://github.com/containers/podman-compose).
I expected errors, and *of course* we had them.

The env is as following:

- `uname -a` returns: `Linux <computer name> 5.15.90.2-microsoft-standard-WSL2 #1 SMP Fri Jun 2 00:45:15 UTC 2023 x86_64 x86_64 x86_64 GNU/Linu`
- WSL2 (pre-release 1.3.11, kernel 5.15.90.2-3)
- Ubuntu 22.04.2 (LTS, fresh one)
- podman 3.4.4, (installed by `apt`)
- podman compose 1.0.6 (latest by now, installed by `pip`)

Let's create a sample *compose* file, and try to let it running using podman compose (create `compose.yml` and then do `podman-compose up`):

```yml
services:
  base:
    image: alpine:latest
    command: sh -c "echo ok"
```

it fails because of two reasons:

1. missing `docker.io` alias in registry config
   
   Solving:
   
    create a file `~/.config/containers/registries.conf` with contents:
    
    ```conf
    unqualified-search-registries=["docker.io"]
    
    [aliases]
    "library"="docker.io/library"
    ```
3. incorrect version of some package (podman 3.4.4 bug):
   ```
   WARN[0000] Error validating CNI config file /etc/cni/net.d/test-net.conflist: [plugin bridge does not support config version "1.0.0" plugin portmap does not support config version "1.0.0" plugin firewall does not support config version "1.0.0" plugin tuning does not support config version "1.0.0"]
   ```

   Solving:
   
   update the lib manually:

    ```sh
    curl -O http://archive.ubuntu.com/ubuntu/pool/universe/g/golang-github-containernetworking-plugins/containernetworking-plugins_1.1.1+ds1-1_amd64.deb
    dpkg -i containernetworking-plugins_1.1.1+ds1-1_amd64.deb
    ```
    
    (see https://bugs.launchpad.net/ubuntu/+source/libpod/+bug/2024394)

