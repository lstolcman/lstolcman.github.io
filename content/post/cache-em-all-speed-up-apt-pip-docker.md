---
title: "Cache'em all! - speeding up apt and other downloads in (not only) docker containers (2024 version)"
date: 2024-02-12T23:07:00+02:00
draft: false
categories: [tech]
tags: [python,apt,pip,docker,podman]
---


When working on couple of ~~Dockerfiles~~Containerfiles, I was having a situation when I had to clear
~~docker~~podman cache (for various reasons), and the indispensable step is to update or download certain
packages from apt. It is often long process, and I was thinking whether there's a way to speed it up or
optimize.

The results of the investigation are below:

# Caching apt get downloads

There are two options of caching:
- mount cache
- apt-cacher-ng

## mount cache

Docker mechanism to cache layers: https://docs.docker.com/build/cache/

Dockerfile extract:
```sh
[...]
RUN rm -f /etc/apt/apt.conf.d/docker-clean # needed in ubuntu to persist cache, otherwise the mount won't work!
RUN --mount=type=cache,mode=0755,target=/var/cache/apt \
    apt-get update \
    && apt-get install -yqq --no-install-recommends \
    git gcc make \
    && rm -rf /var/lib/apt/lists/*
[...]
```

(https://vsupalov.com/buildkit-cache-mount-dockerfile/)



## apt-cacher-ng

`Acquire::http::Proxy` is a config option to set up proxying in apt.
However, since it fails in case proxy is not available, an "improved" version was added - `Acquire::http::Proxy-Auto-Detect`.
`apt-cacher-ng` is a companion to this mechanism - the apt wopuld get pointed to `apt-cacher-ng` instance, but it will fall back to
original source if not available.


`apt-cacher-ng` is a separate app, most probably running in separate container (or as quadlet).
The purpose of the app is to perform *man-in-the-middle* between apt and source servers.
It'll hold the copy of downloaded `.deb`. When the file does not exist, it would get downloaded from
the server. If it is requested again, it is retrieved from local cache, and we'd get the speedup.

It is a nice addition to mount cache mechanism - even if the mount cache did not get hit, we'd have files cached anyway.

Step by step tutorial how to configure:


1. Create background service with name. Note the `--network=host` - the container will be in host network, which should be accessible
    from the other containers.

```sh
docker run --name apt-cacher-ng --init -d --restart=always \
--network=host \
--volume /var/cache/apt-cacher-ng:/var/cache/apt-cacher-ng \
sameersbn/apt-cacher-ng
```

you may want to execute it like

```sh
docker run --name apt-cacher-ng --init -d --restart=always \
--publish 3142:3142 \
--volume /var/cache/apt-cacher-ng:/var/cache/apt-cacher-ng \
sameersbn/apt-cacher-ng
```

this won't not work, because the other containers are not in the same network

Check whether cacher works by visiting a page via browser - go to http://localhost:3142 and you should see a page.
(Replace localhost with ip of your container if needed, e.g. in case of wsl2 or vm).

Once you have cacher set up, lets create a Containerfile which will use it:


example `compose.yaml`:

```dockerfile
services:
  service-1:
    # image: ubuntu
    build:
      # dockerfile: Dockerfile
      context: .
    tty: true
    #  commented out not needed
    # network_mode: bridge
    # hostname: service-2
    # container_name: service-2
    command: " sleep 100000"
    extra_hosts:
      - "host.docker.internal:host-gateway"
```

Note the "extra_hosts" - this is the mechanism which will make sure the container has access to hsot system

read more [1] [2]

example `Dockerfile`:

```dockerfile
FROM docker.io/python:3.11-bookworm

RUN apt-get -q update \
    && apt-get install -qy auto-apt-proxy \
    && apt-get install -qy python3-pip git jq curl unzip gcc
```

Note the important one: the `apt-get install auto-apt-proxy` is separate from installing other apps.
It is imporant, because the apps would be downloaded one by one, and then installed one by one.
Having it as separate command would install it. The subsequent command is going to use `apt-cacher-ng` because the script
will be present in the system.

[auto-pat-proxy source code](https://salsa.debian.org/debian/auto-apt-proxy)


Refs:
[1] https://stackoverflow.com/questions/70725881/what-is-the-equivalent-of-add-host-host-docker-internalhost-gateway-in-a-comp
[2] https://github.com/microsoft/docker/blob/master/docs/examples/apt-cacher-ng.md
[3] https://blog.packagecloud.io/using-apt-cacher-ng-with-ssl-tls/
[4] https://razinj.dev/build-and-run-apt-cacher-ng-proxy-in-docker/
[5] https://medium.com/@TimvanBaarsen/how-to-connect-to-the-docker-host-from-inside-a-docker-container-112b4c71bc66






# Caching python (pip) and/or poetry

There are problems with caching pip. It is related to issues
[like](https://github.com/pypa/pip/issues/6045)
[this](https://stackoverflow.com/questions/67253141/python-pip-riority-order-with-index-url-and-extra-index-url)


Basically, the problem is not yet resolved.

The best way to cache python installs is to do mount cache.

## mount cache -- pip


Not yet there

https://stackoverflow.com/questions/58018300/using-a-pip-cache-directory-in-docker-builds

https://pmac.io/2019/02/multi-stage-dockerfile-and-python-virtualenv/


## mount cache -- poetry

1. configure poetry: `RUN poetry config cache-dir /var/cache/poetry-cache`
    a. you can set env variable as well: https://python-poetry.org/docs/configuration/#cache-dir
2. install but prefix with (buildkit) mount entry: `RUN --mount=type=cache,mode=0755,target=/var/cache/poetry-cache  poetry install`
3. it should work after rebuild with `--no-cache`.


poetry setup for docker
https://github.com/orgs/python-poetry/discussions/1879#discussioncomment-216865
https://github.com/python-poetry/poetry/issues/525#issuecomment-1227231432



# Other stuff, to be cleaned up later

# # docker multistage builds

https://charlesxu.io/docker_multi_stage/

https://medium.com/@tonistiigi/advanced-multi-stage-build-patterns-6f741b852fae

https://www.gasparevitta.com/posts/advanced-docker-multistage-parallel-build-buildkit/

https://pythonspeed.com/articles/multi-stage-docker-python/


## buildkit cache mounts

https://dev.doroshev.com/blog/docker-mount-type-cache/

mode=0755 needed problem:

https://github.com/moby/buildkit/issues/2447

https://stackoverflow.com/questions/61459775/docker-buildkit-mount-type-cache-not-working-why

https://docs.docker.com/build/cache/


https://forums.docker.com/t/how-to-delete-build-cache-buildkit-experimental/70714

https://github.com/moby/buildkit/issues/3155


mount type cache in depth
https://github.com/moby/buildkit/issues/1673


docker network ping by hostname between containers

default bridge - d not work
user defined bridge network - can access via hostname because there is a dns server
https://stackoverflow.com/a/53364345









docker compose for testing the cache


services:
  service-1:
    image: busybox
    # container_name: service-1
    # hostname: service-1
    network_mode: bridge
    command: "ping service-2"
    extra_hosts:
      - "host.docker.internal:host-gateway"
  service-2:
    image: ubuntu
    tty: true
    network_mode: bridge
    # hostname: service-2
    # container_name: service-2
    command: "ping service-1"
    extra_hosts:
      - "host.docker.internal:host-gateway"


networks:
  mynet:
    driver: bridge

https://stackoverflow.com/questions/31324981/how-to-access-host-port-from-docker-container/
https://medium.com/@TimvanBaarsen/how-to-connect-to-the-docker-host-from-inside-a-docker-container-112b4c71bc66
https://www.howtogeek.com/devops/how-to-connect-to-localhost-within-a-docker-container/



https://docs.docker.com/storage/bind-mounts/ mount type=cache missing in examples

and service-1 can ping to host by: ping host.docker.interal
so that, it can access the apt cacher instance available at (localhost):3142 -> host.docker.internal:3142
however, it needs to be specified first:

