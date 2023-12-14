---
title: "Podman compiling from source"
date: 2023-10-18T13:39:00+01:00
draft: false
categories: [tech]
tags: [ubuntu,podman,containers]
---



This post is a draft.



Podman newset version in couple of quick steps



As the title says, if you need newest version of podman, you have to build from source. However, you don't have to build all the necessary 3rd party elements from source (such as go)


# 1.0 Installation of go

```sh
wget https://go.dev/dl/go1.21.3.linux-amd64.tar.gz
```

when follow installtion from

https://go.dev/doc/install


# 2.0 conmon

download, chmod +x, rename and copy to usr local bin

```
wget https://github.com/containers/conmon/releases/download/v2.1.9/conmon.amd64
chmod +x conmon.amd64
mv conmon.amd64 conmon
sudo mv conmon /usr/local/bin
```

## 2.1 instal from source

```
git clone https://github.com/containers/conmon
cd conmon
export GOCACHE="$(mktemp -d)"
make PREFIX=/usr
sudo make podman PREFIX=/usr
```


# 3.0 runc / crun

i'll be using crun, since it is newer, and recommended now

```sh
wget https://github.com/containers/crun/releases/download/1.12/crun-1.12-linux-amd64
chmod +x crun-1.12-linux-amd64
mv crun-1.12-linux-amd64 crun
sudo mv crun /usr/local/bin
```


# 4.0 add config files for podman

copy and paste command from the [tutorial](https://podman.io/docs/installation)




# 5.x needed deps to build podman

for buildeps seccomp (only seccomp)

```
make
gcc
libbtrfs-dev
pkg-config
libseccomp-dev
libdevmapper-dev
libgpgme-dev
uidmap
```

(for systemd - socket activation)

```
libsystemd-dev
```

```
https://github.com/rootless-containers/slirp4netns/releases/download/v1.2.2/slirp4netns-x86_64
https://github.com/containers/fuse-overlayfs/releases/download/v1.13/fuse-overlayfs-x86_64 - for rootless 
```

# 5.0 podman from source

clone podman repo
then checkout to newest tag version (e.g. git checkout v4.7.1)

```sh
git clone https://github.com/containers/podman/
cd podman
make -j4 BUILDTAGS="seccomp systemd" PREFIX=/usr
sudo make install BUILDTAGS="seccomp systemd" PREFIX=/usr
```


next:
enable socket service:

```sh
systemctl status --user podman.socket
systemctl enable --user podman.socket
systemctl start --user podman.socket
systemctl status --user podman.socket
```


do setup cni networking: (config jsons):
https://podman.io/docs/installation#setup-cni-networking


Errors:

Error response from daemon: plugin type="bridge" failed (add): failed to find plugin "bridge" in path [/usr/local/libexec/cni /usr/libexec/cni /usr/local/lib/cni /usr/lib/cni /opt/cni/bin]
➜  gitea sudo apt install c
➜  gitea sudo apt install containernetworking-plugins -y



# for buildx support:

```sh
sudo apt install catatonit
```



and run this at the end:

```
export DOCKER_HOST=unix://$XDG_RUNTIME_DIR/podman/podman.sock

➜  ~ docker-compose ps
NAME      IMAGE     COMMAND   SERVICE   CREATED   STATUS    PORTS
➜  ~ docker-compose up
[+] Building 0.0s (0/0)                                                                                                                                                                                                                                                      docker-container:default [+] Running 1/0
 ✔ Container lukasz-base-1  Created                                                                                                                                                                                                                                                              0.0s Attaching to lukasz-base-1
lukasz-base-1  | ok
lukasz-base-1 exited with code 0
➜  ~ systemctl start --user podman.socket ➜  ~ systemctl status --user podman.socket
➜  ~
➜  ~
```






Note: podman conmon - install via sudo make podman, not from SRC!!!


```sh
➜  ~ docker-compose up
Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?
➜  ~ export GOCACHE="$(mktemp -d)"
➜  ~ export DOCKER_HOST=unix://$XDG_RUNTIME_DIR/podman/podman.sock
➜  ~ docker-compose up
[+] Building 0.0s (0/0)                                                                                                  docker-container:default Attaching to lukasz-base-1
lukasz-base-1  | ok
lukasz-base-1 exited with code 0
➜  ~ cat compose.yml

services:
  base:
    image: alpine:latest
    command: sh -c "echo ok"

➜  ~


➜  conmon git:(main) sudo make podman
install  -d -m 755 /usr/local/libexec/podman
install  -m 755 bin/conmon /usr/local/libexec/podman/conmon
➜  conmon git:(main)



make BUILDTAGS="seccomp" PREFIX=/usr
sudo BUILDTAGS="seccomp" PREFIX=/usr make install



sudo make uninstall BUILDTAGS="seccomp systemd" PREFIX=/usr

```


# podman another storage 

put file in `$HOME/.config/containers/storage.conf`

https://raw.githubusercontent.com/containers/storage/main/storage.conf




# podman and docker-compose, docker alias

For zsh, open `~/.zshrc` and put:

```sh
alias docker=podman
export DOCKER_HOST=unix://$XDG_RUNTIME_DIR/podman/podman.sock
```





# podman system service

podman 4.7.1 contains command for generating socket:

```
podman system service
```

however, it looks like it is removed in podman 4.8

they say it was deprecated in favor of quadlets:

https://github.com/containers/podman/discussions/20218

https://www.redhat.com/sysadmin/quadlet-podman

https://docs.podman.io/en/latest/markdown/podman-system-service.1.html - should be deprecated in docs


