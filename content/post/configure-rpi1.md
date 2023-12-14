---
title: "Configuring raspberry pi 1"
date: 2023-19-19T07:25:00+01:00
draft: false
categories: [tech]
tags: [raspberry-pi,ubuntu]
---

This is a draft notes of my journey to set-up raspberry pi 1 (the oldest one, on arm v6 procesor), and some notes about syncthing



1. Execute parted command to start

```sh
parted
```

2. Suppose, /dev/sda is the device on which to remove the partition. Use the parted commnad with the /dev/sda device as shown below:

```sh
parted /dev/sda
```

3. View partition table to determine the minor number of the partition to remove:

```sh
print
```

4. Remove the partition with the command rm. For example, to remove the partition with minor number 2:

```sh
rm 2
```



creating partition

```sh
(parted) mkpart rootfs-ext4 ext4 0 20gb
Warning: The resulting partition is not properly aligned for best performance: 64s % 2048s != 0s
Ignore/Cancel? c
(parted) mkpart rootfs-ext4 ext4 0% 10%
(parted) print
Model: Crucial_ CT256MX100SSD1 (scsi)
Disk /dev/sda: 256GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:

Number  Start   End     Size    File system  Name         Flags
 1      1049kB  25.6GB  25.6GB  ext4         rootfs-ext4

(parted) mkpart datafs-ext4 ext4 10% 100%
(parted) print
Model: Crucial_ CT256MX100SSD1 (scsi)
Disk /dev/sda: 256GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:

Number  Start   End     Size    File system  Name         Flags
 1      1049kB  25.6GB  25.6GB  ext4         rootfs-ext4
 2      25.6GB  256GB   230GB   ext4         datafs-ext4

(parted)
```


nastepnie formatujemy partcje:


https://www.gnu.org/software/parted/manual/html_node/mkpart.html

```sh
sudo mkfs.vfat -L boot-firmware /dev/sda1
sudo mkfs.ext4 -L rootfs-ext4 /dev/sda2
sudo mkfs.ext4 -L data-ext4 /dev/sda3

rsync -avx --exclude /mnt / /mnt/rootfs-ext4

lukasz@rpi-fat:/boot $ blkid
/dev/mmcblk0p2: LABEL="rootfs" UUID="b8dc102a-7e9b-4989-ab46-b5a05eae6074" BLOCK_SIZE="4096" TYPE="ext4" PARTUUID="1e764d14-02"
/dev/mmcblk0p1: LABEL_FATBOOT="bootfs" LABEL="bootfs" UUID="98C6-42F3" BLOCK_SIZE="512" TYPE="vfat" PARTUUID="1e764d14-01"
/dev/sda3: LABEL="data-ext4" UUID="d29d35f8-e87a-411a-b146-4800a37cd464" BLOCK_SIZE="4096" TYPE="ext4" PARTLABEL="data-ext4" PARTUUID="dcae8df1-0ab2-4fd1-a75f-ed2605cfd9ac"
/dev/sda2: LABEL="rootfs-ext4" UUID="4cf8e22c-0d26-4f8f-ae93-7e527a2f0d49" BLOCK_SIZE="4096" TYPE="ext4" PARTLABEL="rootfs-ext4" PARTUUID="2e0e30e5-373c-4878-a1c3-d32148ff5b2a"
/dev/sda1: SEC_TYPE="msdos" UUID="3ECA-4E56" BLOCK_SIZE="512" TYPE="vfat" PARTLABEL="boot-firmware" PARTUUID="d5be54cd-4897-4f6e-9fd5-03ea07e7494c"
lukasz@rpi-fat:/boot $



lukasz@rpi-fat:/boot $ cat /boot/cmdline.txt
console=serial0,115200 console=tty1 root=PARTUUID=1e764d14-02 rootfstype=ext4 fsck.repair=yes rootwait cfg80211.ieee80211_regdom=PL
console=serial0,115200 console=tty1 root=PARTUUID=2e0e30e5-373c-4878-a1c3-d32148ff5b2a rootfstype=ext4 fsck.repair=yes rootwait cfg80211.ieee80211_regdom=PL
lukasz@rpi-fat:/boot $ sync
```

you may enter a error mesasge that the target is busyu
you can find out which process blocks it:

```sh
sudo lsof /Path/to/target
```

and then `kill -9 <pid>`




adding partition
so created `/data partition`
to begin, create mount dir: `sudo mkdir /mnt/data`
one ned to add it to `/etc/fstab`:
just copy entry, change partuuid to correct one and set directory to `/mnt/data`







changing swap size

1. Temporarily Stop Swap:

```sh
sudo dphys-swapfile swapoff
```

2. Modify the size of the swap

As root, edit the file `/etc/dphys-swapfile` and modify the variable `CONF_SWAPSIZE` to `1024`:

```sh
CONF_SWAPSIZE=1024
```

Using a command such as:
```sh

sudo nano /etc/dphys-swapfile
```

3. Initialize Swap File

```sh
sudo dphys-swapfile setup
```

4. Start Swap

```sh
sudo dphys-swapfile swapon

kernel.unprivileged_userns_clone=1
```



## syncthing - how to install

instruction:
https://apt.syncthing.net/

after:
run syncthing:

```sh
syncthing
```

so the config will be created
edit config:

```sh
sudo vim ~/.config/syncthing/config.xml
```

edit `<address>127.0.0.1:8384</address>` to `<address>0.0.0.0:8384</address>`

then enable:

```sh
sudo systemctl enable syncthing@<username>.service
```

https://docs.syncthing.net/users/autostart.html
(system service)


after reboot, enter in browser
`<ip>:8384`, and add password


https://linuxhint.com/synchronizing-files-raspberry-pi-syncthing/



# podman compile from src - rough notes, may not work




`wget https://go.dev/dl/go1.21.3.linux-armv6l.tar.gz`

https://askubuntu.com/questions/720260/updating-golang-on-ubuntu


## build of podman
podman needs couple of parts. most of them can be used compiled. podman itself needs to be built from src

### conmon

```sh
git clone https://github.com/containers/conmon
cd conmon
export GOCACHE="$(mktemp -d)"
make
```

done




### podman

```sh
git clone https://github.com/containers/podman/
cd podman
make BUILDTAGS=""
```

### runc

```sh
git clone https://github.com/opencontainers/runc.git --depth=1 $GOPATH/src/github.com/opencontainers/runc
cd $GOPATH/src/github.com/opencontainers/runc
make BUILDTAGS=""
```




