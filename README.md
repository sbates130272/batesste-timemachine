# batesste-timemachine

## Introduction

This repository is a docker-based implementation of an Apple [Time
Machine][timeref] service that can be run on a host Linux server. This
service ties in with [systemd][systemdref] and [docker-compose][dc-ref].

The docker-compose.yml file in this repo is *heavily* based on the
great work by [mbently][mbentlyref].

## Install

The repo contains a batesste-timemachine.service file for systemd. You
need to perform the following steps in order to install it
sucessfully on Linux based systems.

1. Make sure docker and docker-compose are installed.
1. Choose which physical block device(s) you wish to use for the
   timemachine data store and ensure this is mapped/mounted to
   /mnt/timemachine-disk. Be sure to add this entry to your fstab so it
   persists across reboots.
1. ```sudo mkdir -p /opt/batesste-timemachine/```
1. ```sudo cp batesste-timemachine-compose.yml /opt/batesste-timemachine/```
1. ```sudo cp batesste-timemachine.service /usr/lib/systemd/system/```
1. ```sudo systemctl daemon-reload```
1. ```sudo systemctl enable batesste-timemachine```
1. ```sudo systemctl start batesste-timemachine```

## Usage

Once you have performed the steps above you should be able to see the
timemachine service exposed via the hostname of the physical host you
are running on. It is possible to advertise a different hostname (see
the modifications section below).

## Modification

There is quite a bit of modification possible and most of this can be
done by over-riding the defaults in the
batesste-timemachine-compose.yml file. For more information on this
see the documentation on the [docker image][mbentlyref]. Possible
modifications include:

1. The advertised hostname.
1. The username and password.
1. The location of the backing store for timecapsule data.

## Volumes

The docker-compose uses docker volumes to store certain persistent
information related to the SMB service (not the actual user
backup-data). If you migrate this service to a new host you will need
to also migrate these volumes.

## Mounting

As an example here is the fstab entry for my system. You can use blkid
to obtain the UUID for your block device.
```
/dev/disk/by-uuid/3f2929a9-c2c7-4d62-ac0c-e8c6ed45cf57 /mnt/timemachine-disk ext4 defaults 0 1
```
Note my system has no RAID and you might want RAID if you want to
ensure you backups survive a disk failure.

[timeref]: https://support.apple.com/en-ca/HT201250
[dc-ref]: https://docs.docker.com/compose/reference/
[systemdref]: https://manpages.ubuntu.com/manpages/jammy/man5/systemd.service.5.html
[mbentlyref]: https://hub.docker.com/r/mbentley/timemachine
