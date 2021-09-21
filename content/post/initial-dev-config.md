---
title: "Initial dev config of cmder and git"
date: 2021-09-21T21:39:59+02:00
draft: false
---


# Git

A version control, which anybody should be familiar with (at least when one is a developer ;)).

The configuration is simple two steps - ssh keys generation, and `.gitconfig`

## generating ssh keys

Covered in [Cmder minimal](#cmder-minimal) section

## `.gitconfig`

Basic config with [aliases](https://git-scm.com/book/en/v2/Git-Basics-Git-Aliases), [additional tools](https://gitextensions.github.io/)....

```sh
[core]
	editor = \"C:/tools/GitExtensions/GitExtensions.exe\" fileeditor
[user]
	email = ...
	name = ...
[alias]
	co = checkout
	ci = commit
	st = status
	br = branch
[pull]
	rebase = false
[fetch]
	prune = false
[rebase]
	autoStash = false

```


# [Cmder](https://cmder.net/)...
...is one of a nice applications, which bnrings you more closer to linux terminal look-and-feel.
It is kind of obligatory software for me, wne I do setup new instance of a system, especially for developing.

Besides being great, its behavior can be improved a little bit - which I will divide into several sections:


## ssh config for git

It depends if you have installed cmder minimal, or cmder full (with git).

### cmder minimal {#cmder-minimal}

open `C:\tools\cmder\config\user_profile.cmd` and "uncomment this to have the ssh agent load when cmder starts":
```
:: call "%GIT_INSTALL_ROOT%/cmd/start-ssh-agent.cmd" /k exit
```

Then,
[generate ssh key](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent#generating-a-new-ssh-key)
and
[add it to ssh-agent](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent#adding-your-ssh-key-to-the-ssh-agent)
ssh key using usial methods


### cmder full (with git)

You do not have to do anything, unless you have keys specified in standard file format, which are:

(specified in `<cmder main directory>\vendor\git-for-windows\mingw64\share\git-gui\lib\sshkey.tcl`)
```
~/.ssh/id_dsa.pub
~/.ssh/id_ecdsa.pub
~/.ssh/id_ed25519.pub
~/.ssh/id_rsa.pub
~/.ssh/identity.pub
```


# clink config for bash-like history under up/down arrow

A must-have feature, for which I had to search a lot.
Fortunately, it is possible with some additional configuration (not sure why it is not a default).

To start with, execute `clink info` to find paths, where clink is looking for its configuration:
![Cliink info output](/images/clink-info.png)

In my case, I've chosen `c:\Users\Lukasz\.inputrc` (as it is distinguished by "(exists)").
The contents is as folows:

```
set history-point-at-end-of-anchored-search on

"\e[A": history-search-backward     # arrow up
"\e[B": history-search-forward      # arrow down

```

Those two urls makes more clear what are these lines about:

- https://github.com/cmderdev/cmder/issues/2184#issuecomment-888051243
- https://tiswww.cwru.edu/php/chet/readline/readline.html#SEC15


# cmder default startup directory

The easiest is to use settings:

![Conemu startup dir dialog](/images/conemu-startup-dir.png)

The config file (`cmder\vendor\conemu-maximus5\ConEmu.xml`) can work it out too, but it is xml, so I did not bother...
