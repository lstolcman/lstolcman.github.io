---
title: "Multiple WSL environments"
date: 2022-02-11T19:55:00+01:00
draft: false
categories: [tech]
tags: [wsl]
---


Not so easy way to figure out, but there is a way to have multiple wsl instances.

Assuming you do not have any distributions installed (check via `wsl --list -v`), install the clean one:

```sh
wsl --install Ubuntu-22.04
```

1. Enter the instance and do the necessary configuration (it'll be the default for your instances)

2. Then, export the instance

```ssh
wsl --export Ubuntu-22.04 c:\dev\wsl\wsl-clean-instance-ubuntu2204
```

3. If you like to create a new instance, type:

```sh
wsl --import myinstance c:\dev\wsl\instances\myinstance c:\dev\wsl\wsl-clean-instance-ubuntu2204
```

and enter into instance:

```sh
wsl -d myinstance -u myusername
```

where `myusername` is the user name you entered in 1.
