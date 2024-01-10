---
title: "podman and podman-compose in Ubuntu 22.04.3 (WSL2)"
date: 2024-01-11T00:07:00+02:00
draft: false
categories: [tech]
tags: [ubuntu,podman,containers,wsl2]
---


Ubuntu reports fairly old version od podman in its default apt registry.

Here I will present how to install podman 4.6.2 from separate apt.


# System

I am using wsl2 with ubuntu 22 lts:

```sh
➜  lsb_release -a
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 22.04.3 LTS
Release:        22.04
Codename:       jammy
```

# Adding apt repository

The source is [here](https://software.opensuse.org//download.html?project=devel%3Akubic%3Alibcontainers%3Aunstable&package=podman).
Execute following lines in a console:

```sh
echo 'deb http://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/unstable/xUbuntu_22.04/ /' | sudo tee /etc/apt/sources.list.d/devel:kubic:libcontainers:unstable.list
curl -fsSL https://download.opensuse.org/repositories/devel:kubic:libcontainers:unstable/xUbuntu_22.04/Release.key | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/devel_kubic_libcontainers_unstable.gpg > /dev/null
sudo apt -y update
sudo apt -y install podman
```

Expected output:

```sh
➜  ~ echo 'deb http://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/unstable/xUbuntu_22.04/ /' | sudo tee /etc/apt/sources.list.d/devel:kubic:libcontainers:unstable.list
SL https://downldeb http://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/unstable/xUbuntu_22.04/ /
oad.open%
➜  ~ curl -fsSL https://download.opensuse.org/repositories/devel:kubic:libcontainers:unstable/xUbuntu_22.04/Release.key | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/devel_kubic_libcontainers_unstable.gpg > /dev/null
sudo apt -y update
sudo apt -y install podman

➜  ~ sudo apt -y update
sudo apt -y install podman

Hit:1 http://archive.ubuntu.com/ubuntu jammy InRelease
Get:2 http://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/unstable/xUbuntu_22.04  InRelease [1262 B]
Get:3 http://archive.ubuntu.com/ubuntu jammy-updates InRelease [119 kB]
Get:4 http://security.ubuntu.com/ubuntu jammy-security InRelease [110 kB]
Hit:5 http://archive.ubuntu.com/ubuntu jammy-backports InRelease
Get:6 http://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/unstable/xUbuntu_22.04  Packages [9067 B]
Get:7 http://archive.ubuntu.com/ubuntu jammy-updates/main amd64 Packages [1277 kB]
Get:8 http://archive.ubuntu.com/ubuntu jammy-updates/main Translation-en [261 kB]
Get:9 http://archive.ubuntu.com/ubuntu jammy-updates/restricted amd64 Packages [1272 kB]
Get:10 http://archive.ubuntu.com/ubuntu jammy-updates/restricted Translation-en [207 kB]
Get:11 http://archive.ubuntu.com/ubuntu jammy-updates/universe amd64 Packages [1022 kB]
Get:12 http://archive.ubuntu.com/ubuntu jammy-updates/universe Translation-en [227 kB]
Get:13 http://security.ubuntu.com/ubuntu jammy-security/main amd64 Packages [1062 kB]
Get:14 http://security.ubuntu.com/ubuntu jammy-security/main Translation-en [201 kB]
Get:15 http://security.ubuntu.com/ubuntu jammy-security/restricted amd64 Packages [1244 kB]
Get:16 http://security.ubuntu.com/ubuntu jammy-security/restricted Translation-en [203 kB]
Get:17 http://security.ubuntu.com/ubuntu jammy-security/universe amd64 Packages [824 kB]
Fetched 8040 kB in 3s (2669 kB/s)
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
5 packages can be upgraded. Run 'apt list --upgradable' to see them.
➜  ~ sudo apt -y install podman
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  aardvark-dns catatonit conmon containers-common criu crun fuse-overlayfs libgpgme11 libnet1 libprotobuf-c1 libprotobuf23 libslirp0 libyajl2 netavark podman-gvproxy python3-protobuf slirp4netns uidmap
Suggested packages:
  qemu-user-static
The following NEW packages will be installed:
  aardvark-dns catatonit conmon containers-common criu crun fuse-overlayfs libgpgme11 libnet1 libprotobuf-c1 libprotobuf23 libslirp0 libyajl2 netavark podman podman-gvproxy python3-protobuf slirp4netns uidmap
0 upgraded, 19 newly installed, 0 to remove and 5 not upgraded.
Need to get 47.9 MB of archives.
After this operation, 139 MB of additional disk space will be used.
Get:1 http://archive.ubuntu.com/ubuntu jammy/universe amd64 catatonit amd64 0.1.7-1 [307 kB]
Get:7 http://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/unstable/xUbuntu_22.04  containers-common 4:1-0ubuntu22.04+obs20.58 [73.3 kB]
Get:2 http://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/unstable/xUbuntu_22.04  aardvark-dns 1.6.0-0ubuntu22.04+obs34.25 [3012 kB]
Get:11 http://archive.ubuntu.com/ubuntu jammy/main amd64 libslirp0 amd64 4.6.1-1build1 [61.5 kB]
Get:12 http://archive.ubuntu.com/ubuntu jammy-updates/main amd64 libprotobuf23 amd64 3.12.4-1ubuntu7.22.04.1 [877 kB]
Get:13 http://archive.ubuntu.com/ubuntu jammy-updates/main amd64 python3-protobuf amd64 3.12.4-1ubuntu7.22.04.1 [357 kB]
Get:14 http://archive.ubuntu.com/ubuntu jammy/main amd64 libnet1 amd64 1.1.6+dfsg-3.1build3 [46.9 kB]
Get:15 http://archive.ubuntu.com/ubuntu jammy-updates/main amd64 libprotobuf-c1 amd64 1.3.3-1ubuntu2.1 [20.3 kB]
Get:16 http://archive.ubuntu.com/ubuntu jammy/universe amd64 criu amd64 3.16.1-2 [723 kB]
Get:17 http://archive.ubuntu.com/ubuntu jammy-updates/main amd64 libyajl2 amd64 2.1.0-3ubuntu0.22.04.1 [21.0 kB]
Get:18 http://archive.ubuntu.com/ubuntu jammy-updates/main amd64 libgpgme11 amd64 1.16.0-1.2ubuntu4.1 [136 kB]
Get:19 http://archive.ubuntu.com/ubuntu jammy-updates/universe amd64 uidmap amd64 1:4.8.1-2ubuntu2.1 [22.4 kB]
Get:3 http://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/unstable/xUbuntu_22.04  conmon 2:2.1.10-0ubuntu22.04+obs18.4 [122 kB]
Get:4 http://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/unstable/xUbuntu_22.04  slirp4netns 1.2.2-0ubuntu22.04+obs12.31 [135 kB]
Get:5 http://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/unstable/xUbuntu_22.04  crun 101:1.12-0ubuntu22.04+obs65.18 [893 kB]
Get:6 http://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/unstable/xUbuntu_22.04  netavark 1.3.0-0ubuntu22.04+obs22.9 [3813 kB]
Get:8 http://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/unstable/xUbuntu_22.04  fuse-overlayfs 1.13-0ubuntu22.04+obs22.32 [176 kB]
Get:9 http://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/unstable/xUbuntu_22.04  podman 4:4.6.2-0ubuntu22.04+obs81.12 [30.0 MB]
Get:10 http://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/unstable/xUbuntu_22.04  podman-gvproxy 4:4.6.2-0ubuntu22.04+obs81.12 [7086 kB]
Fetched 47.9 MB in 2s (21.8 MB/s)
Selecting previously unselected package aardvark-dns.
(Reading database ... 25682 files and directories currently installed.)
Preparing to unpack .../00-aardvark-dns_1.6.0-0ubuntu22.04+obs34.25_amd64.deb ...
Unpacking aardvark-dns (1.6.0-0ubuntu22.04+obs34.25) ...
Selecting previously unselected package catatonit.
Preparing to unpack .../01-catatonit_0.1.7-1_amd64.deb ...
Unpacking catatonit (0.1.7-1) ...
Selecting previously unselected package conmon.
Preparing to unpack .../02-conmon_2%3a2.1.10-0ubuntu22.04+obs18.4_amd64.deb ...
Unpacking conmon (2:2.1.10-0ubuntu22.04+obs18.4) ...
Selecting previously unselected package libslirp0:amd64.
Preparing to unpack .../03-libslirp0_4.6.1-1build1_amd64.deb ...
Unpacking libslirp0:amd64 (4.6.1-1build1) ...
Selecting previously unselected package slirp4netns.
Preparing to unpack .../04-slirp4netns_1.2.2-0ubuntu22.04+obs12.31_amd64.deb ...
Unpacking slirp4netns (1.2.2-0ubuntu22.04+obs12.31) ...
Selecting previously unselected package libprotobuf23:amd64.
Preparing to unpack .../05-libprotobuf23_3.12.4-1ubuntu7.22.04.1_amd64.deb ...
Unpacking libprotobuf23:amd64 (3.12.4-1ubuntu7.22.04.1) ...
Selecting previously unselected package python3-protobuf.
Preparing to unpack .../06-python3-protobuf_3.12.4-1ubuntu7.22.04.1_amd64.deb ...
Unpacking python3-protobuf (3.12.4-1ubuntu7.22.04.1) ...
Selecting previously unselected package libnet1:amd64.
Preparing to unpack .../07-libnet1_1.1.6+dfsg-3.1build3_amd64.deb ...
Unpacking libnet1:amd64 (1.1.6+dfsg-3.1build3) ...
Selecting previously unselected package libprotobuf-c1:amd64.
Preparing to unpack .../08-libprotobuf-c1_1.3.3-1ubuntu2.1_amd64.deb ...
Unpacking libprotobuf-c1:amd64 (1.3.3-1ubuntu2.1) ...
Selecting previously unselected package criu.
Preparing to unpack .../09-criu_3.16.1-2_amd64.deb ...
Unpacking criu (3.16.1-2) ...
Selecting previously unselected package libyajl2:amd64.
Preparing to unpack .../10-libyajl2_2.1.0-3ubuntu0.22.04.1_amd64.deb ...
Unpacking libyajl2:amd64 (2.1.0-3ubuntu0.22.04.1) ...
Selecting previously unselected package crun.
Preparing to unpack .../11-crun_101%3a1.12-0ubuntu22.04+obs65.18_amd64.deb ...
Unpacking crun (101:1.12-0ubuntu22.04+obs65.18) ...
Selecting previously unselected package netavark.
Preparing to unpack .../12-netavark_1.3.0-0ubuntu22.04+obs22.9_amd64.deb ...
Unpacking netavark (1.3.0-0ubuntu22.04+obs22.9) ...
Selecting previously unselected package containers-common.
Preparing to unpack .../13-containers-common_4%3a1-0ubuntu22.04+obs20.58_amd64.deb ...
Unpacking containers-common (4:1-0ubuntu22.04+obs20.58) ...
Selecting previously unselected package fuse-overlayfs.
Preparing to unpack .../14-fuse-overlayfs_1.13-0ubuntu22.04+obs22.32_amd64.deb ...
Unpacking fuse-overlayfs (1.13-0ubuntu22.04+obs22.32) ...
Selecting previously unselected package libgpgme11:amd64.
Preparing to unpack .../15-libgpgme11_1.16.0-1.2ubuntu4.1_amd64.deb ...
Unpacking libgpgme11:amd64 (1.16.0-1.2ubuntu4.1) ...
Selecting previously unselected package uidmap.
Preparing to unpack .../16-uidmap_1%3a4.8.1-2ubuntu2.1_amd64.deb ...
Unpacking uidmap (1:4.8.1-2ubuntu2.1) ...
Selecting previously unselected package podman.
Preparing to unpack .../17-podman_4%3a4.6.2-0ubuntu22.04+obs81.12_amd64.deb ...
Unpacking podman (4:4.6.2-0ubuntu22.04+obs81.12) ...
Selecting previously unselected package podman-gvproxy.
Preparing to unpack .../18-podman-gvproxy_4%3a4.6.2-0ubuntu22.04+obs81.12_amd64.deb ...
Unpacking podman-gvproxy (4:4.6.2-0ubuntu22.04+obs81.12) ...
Setting up uidmap (1:4.8.1-2ubuntu2.1) ...
Setting up libyajl2:amd64 (2.1.0-3ubuntu0.22.04.1) ...
Setting up libprotobuf23:amd64 (3.12.4-1ubuntu7.22.04.1) ...
Setting up libnet1:amd64 (1.1.6+dfsg-3.1build3) ...
Setting up libprotobuf-c1:amd64 (1.3.3-1ubuntu2.1) ...
Setting up libgpgme11:amd64 (1.16.0-1.2ubuntu4.1) ...
Setting up conmon (2:2.1.10-0ubuntu22.04+obs18.4) ...
Setting up catatonit (0.1.7-1) ...
Setting up podman-gvproxy (4:4.6.2-0ubuntu22.04+obs81.12) ...
Setting up aardvark-dns (1.6.0-0ubuntu22.04+obs34.25) ...
Setting up libslirp0:amd64 (4.6.1-1build1) ...
Setting up fuse-overlayfs (1.13-0ubuntu22.04+obs22.32) ...
Setting up python3-protobuf (3.12.4-1ubuntu7.22.04.1) ...
Setting up slirp4netns (1.2.2-0ubuntu22.04+obs12.31) ...
Setting up netavark (1.3.0-0ubuntu22.04+obs22.9) ...
Setting up criu (3.16.1-2) ...
Setting up crun (101:1.12-0ubuntu22.04+obs65.18) ...
Setting up containers-common (4:1-0ubuntu22.04+obs20.58) ...
Setting up podman (4:4.6.2-0ubuntu22.04+obs81.12) ...
Processing triggers for libc-bin (2.35-0ubuntu3.5) ...
/sbin/ldconfig.real: /usr/lib/wsl/lib/libcuda.so.1 is not a symbolic link

Processing triggers for man-db (2.10.2-1) ...
➜  ~ 
```

# Setting up podman socket service

The detailed instruction is [here](https://github.com/containers/podman/tree/28b5a6e5f08688ba0f31cb4f4ee25414c6bd7516/contrib/systemd)

```sh
systemctl --user status podman.socket podman.service
systemctl --user enable podman.socket
systemctl --user start podman.socket
systemctl --user status podman.socket podman.service
```


Expected output:

```sh
➜  ~ systemctl --user status podman.socket podman.service
○ podman.socket - Podman API Socket
     Loaded: loaded (/usr/lib/systemd/user/podman.socket; disabled; vendor preset: enabled)
     Active: inactive (dead)
   Triggers: ● podman.service
       Docs: man:podman-system-service(1)
     Listen: /run/user/1000/podman/podman.sock (Stream)

○ podman.service - Podman API Service
     Loaded: loaded (/usr/lib/systemd/user/podman.service; disabled; vendor preset: enabled)
     Active: inactive (dead)
TriggeredBy: ○ podman.socket
       Docs: man:podman-system-service(1)
➜  ~ systemctl --user enable podman.socket
systemctl --userCreated symlink /home/lukasz/.config/systemd/user/sockets.target.wants/podman.socket → /usr/lib/systemd/user/podman.socket.
 status podman.socket podman.service
➜  ~ systemctl --user start podman.socket
➜  ~ systemctl --user status podman.socket podman.service
● podman.socket - Podman API Socket
     Loaded: loaded (/usr/lib/systemd/user/podman.socket; enabled; vendor preset: enabled)
     Active: active (listening) since Wed 2024-01-10 23:40:16 CET; 51ms ago
   Triggers: ● podman.service
       Docs: man:podman-system-service(1)
     Listen: /run/user/1000/podman/podman.sock (Stream)
     CGroup: /user.slice/user-1000.slice/user@1000.service/app.slice/podman.socket

Jan 10 23:40:16 DESKTOP-H6UMH56 systemd[573]: Listening on Podman API Socket.

○ podman.service - Podman API Service
     Loaded: loaded (/usr/lib/systemd/user/podman.service; disabled; vendor preset: enabled)
     Active: inactive (dead)
TriggeredBy: ● podman.socket
       Docs: man:podman-system-service(1)
➜  ~ 
```

# downloading `docker-compose`

`docker-compose` is a singlefile binary. The following script downloads it into local bin and makes it executable:


```sh
wget https://github.com/docker/compose/releases/download/v2.23.3/docker-compose-linux-x86_64 -O /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

Expected output:

```sh
➜  ~ wget https://github.com/docker/compose/releases/download/v2.23.3/docker-compose-linux-x86_64 -O /usr/local/bin/docker-compose
/usr/local/bin/docker-compose: Permission denied
➜  ~ sudo wget https://github.com/docker/compose/releases/download/v2.23.3/docker-compose-linux-x86_64 -O /usr/local/bin/docker-compose
--2024-01-11 00:15:26--  https://github.com/docker/compose/releases/download/v2.23.3/docker-compose-linux-x86_64
Resolving github.com (github.com)... 140.82.121.3
Connecting to github.com (github.com)|140.82.121.3|:443... connected.
HTTP request sent, awaiting response... 302 Found
Location: https://objects.githubusercontent.com/github-production-release-asset-2e65be/15045751/ae54f861-b9e0-499d-91b4-e3e7e1e5c25a?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAVCODYLSA53PQK4ZA%2F20240110%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20240110T231507Z&X-Amz-Expires=300&X-Amz-Signature=66b23d02f01b28b039e75dcc722bde71ec0ec8fa7ff74d0a665c39f527e15104&X-Amz-SignedHeaders=host&actor_id=0&key_id=0&repo_id=15045751&response-content-disposition=attachment%3B%20filename%3Ddocker-compose-linux-x86_64&response-content-type=application%2Foctet-stream [following]
--2024-01-11 00:15:27--  https://objects.githubusercontent.com/github-production-release-asset-2e65be/15045751/ae54f861-b9e0-499d-91b4-e3e7e1e5c25a?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAVCODYLSA53PQK4ZA%2F20240110%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20240110T231507Z&X-Amz-Expires=300&X-Amz-Signature=66b23d02f01b28b039e75dcc722bde71ec0ec8fa7ff74d0a665c39f527e15104&X-Amz-SignedHeaders=host&actor_id=0&key_id=0&repo_id=15045751&response-content-disposition=attachment%3B%20filename%3Ddocker-compose-linux-x86_64&response-content-type=application%2Foctet-stream
Resolving objects.githubusercontent.com (objects.githubusercontent.com)... 185.199.109.133, 185.199.108.133, 185.199.110.133, ...
Connecting to objects.githubusercontent.com (objects.githubusercontent.com)|185.199.109.133|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 59699273 (57M) [application/octet-stream]
Saving to: ‘/usr/local/bin/docker-compose’

/usr/local/bin/docker-compose                                             100%[===================================================================================================================================================================================>]  56.93M  31.7MB/s    in 1.8s

2024-01-11 00:15:29 (31.7 MB/s) - ‘/usr/local/bin/docker-compose’ saved [59699273/59699273]

➜  ~ sudo chmod +x /usr/local/bin/docker-compose
➜  ~
```

# example run of compose

```sh
export DOCKER_HOST=unix://$XDG_RUNTIME_DIR/podman/podman.sock

cat > compose.yml<< EOF
services:
  base:
    image: alpine:latest
    command: sh -c "echo ok"
EOF

cat compose.yml

docker-compose up

```

Example output:

```sh
➜  ~ export DOCKER_HOST=unix://$XDG_RUNTIME_DIR/podman/podman.sock
➜  ~ cat > compose.yml<< EOF
heredoc> services:
heredoc>   base:
heredoc>     image: alpine:latest
heredoc>     command: sh -c "echo ok"
heredoc> EOF
➜  ~ cat compose.yml
services:
  base:
    image: alpine:latest
    command: sh -c "echo ok"
➜  ~ docker-compose up
[+] Running 3/3
 ✔ base 2 layers [⣿⣿]      0B/0B      Pulled                                                                                                                                                                                                                                                     2.3s
   ✔ 661ff4d9561e Download complete                                                                                                                                                                                                                                                              0.4s
   ✔ f8c20f8bbcb6 Download complete                                                                                                                                                                                                                                                              0.0s [+] Running 1/0
 ✔ Container lukasz-base-1  Created                                                                                                                                                                                                                                                              0.1s Attaching to base-1
base-1  | ok
base-1 exited with code 0
➜  ~
```


# possible errors

```sh

➜  flask git:(master) ✗ docker-compose up
[+] Building 7.5s (1/1) FINISHED                                                                                                                                                                                                                                             docker-container:default  => ERROR [web internal] booting buildkit                                                                                                                                                                                                                                                        7.5s  => => pulling image moby/buildkit:buildx-stable-1                                                                                                                                                                                                                                               5.8s  => => creating container buildx_buildkit_default                                                                                                                                                                                                                                                1.7s ------
 > [web internal] booting buildkit:
------
Error response from daemon: crun: creating cgroup directory `/sys/fs/cgroup/systemd/docker/buildx/libpod-f8c765ccab51b09b5d3b676cfc9ebeda351e481ee131e5397acb61eb312c41d3`: No such file or directory: OCI runtime attempted to invoke a command that was not found
```

You should disable buildkit by adding `DOCKER_BUILDKIT=0` before command (or: do `export DOCKER_BUILDKIT=0`, best if put this in your bashrc or zshrc file):

```sh

➜  flask git:(master) ✗ DOCKER_BUILDKIT=0 docker-compose up
Sending build context to Docker daemon     597B
STEP 1/8: FROM python:3.10-alpine AS builder
Resolving %!q(<nil>) to docker.io (enforced by caller)
Trying to pull docker.io/library/python:3.10-alpine...
Getting image source signatures
Copying blob sha256:8c653295a5ef963995a580080525696a67e2671956a9c4c55e8b699d447cf790
Copying blob sha256:661ff4d9561e3fd050929ee5097067c34bafc523ee60f5294a37fd08056a73ca
Copying blob sha256:4a3bf9ff3965616e6c4ba6917c0cb75281baf44e209eea3b48253f7e94b76058
Copying blob sha256:44cda88cd45dc00d3349d343b46e92f0d55daa059d276f3ecf860c174bbedf81
Copying blob sha256:48c91875651ed05bd18926dbdaf7dcad60d34762cd2ab2ab80954031cdf851f7
Copying config sha256:5fee4875cce7bbf3917b479372bd48da466530710f134ea5bd5f1887209e66a8
Writing manifest to image destination
STEP 2/8: WORKDIR /app
--> 9631821fe1fc
STEP 3/8: COPY requirements.txt /app
--> de7b7b4f4526
STEP 4/8: RUN --mount=type=cache,target=/root/.cache/pip     pip3 install -r requirements.txt
Collecting flask
  Using cached flask-3.0.0-py3-none-any.whl (99 kB)
Collecting Jinja2>=3.1.2
  Downloading Jinja2-3.1.3-py3-none-any.whl (133 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 133.2/133.2 kB 895.2 kB/s eta 0:00:00
Collecting Werkzeug>=3.0.0
  Using cached werkzeug-3.0.1-py3-none-any.whl (226 kB)
Collecting click>=8.1.3
  Using cached click-8.1.7-py3-none-any.whl (97 kB)
Collecting blinker>=1.6.2
  Using cached blinker-1.7.0-py3-none-any.whl (13 kB)
Collecting itsdangerous>=2.1.2
  Using cached itsdangerous-2.1.2-py3-none-any.whl (15 kB)
Collecting MarkupSafe>=2.0
  Using cached MarkupSafe-2.1.3-cp310-cp310-musllinux_1_1_x86_64.whl (29 kB)
Installing collected packages: MarkupSafe, itsdangerous, click, blinker, Werkzeug, Jinja2, flask
Successfully installed Jinja2-3.1.3 MarkupSafe-2.1.3 Werkzeug-3.0.1 blinker-1.7.0 click-8.1.7 flask-3.0.0 itsdangerous-2.1.2
WARNING: Running pip as the 'root' user can result in broken permissions and conflicting behaviour with the system package manager. It is recommended to use a virtual environment instead: https://pip.pypa.io/warnings/venv

[notice] A new release of pip is available: 23.0.1 -> 23.3.2
[notice] To update, run: pip install --upgrade pip
--> 5fa5a6ed9db1
STEP 5/8: COPY . /app
--> 046580714195
STEP 6/8: ENTRYPOINT ["python3"]
--> 689fe676d1e5
STEP 7/8: CMD ["app.py"]
--> 0e1030fb2f40
STEP 8/8: LABEL "com.docker.compose.image.builder"="classic"
COMMIT docker.io/library/flask-web
--> 8b0bd6a10c51
Successfully tagged docker.io/library/flask-web:latest
8b0bd6a10c5162b959052946c2bed827baf0193d2bb2b050c34aa1d17a2707bf
Successfully built 8b0bd6a10c51
Successfully tagged flask-web
[+] Running 2/2
 ✔ Network flask_default  Created                                                                                                                                                                                                                                                                0.0s  ✔ Container flask-web-1  Created                                                                                                                                                                                                                                                                0.1s Attaching to web-1
web-1  |  * Serving Flask app 'app'
web-1  |  * Debug mode: off
web-1  | WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
web-1  |  * Running on all addresses (0.0.0.0)
web-1  |  * Running on http://127.0.0.1:8000
web-1  |  * Running on http://10.89.1.4:8000
web-1  | Press CTRL+C to quit
^CGracefully stopping... (press Ctrl+C again to force)
[+] Stopping 1/1
 ✔ Container flask-web-1  Stopped                                                                                                                                                                                                                                                                1.2s web-1 exited with code 137
canceled
➜  flask git:(master) ✗
```
