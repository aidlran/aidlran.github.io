---
layout: post
title: Home Server October 2022 Update Process
date: 2022-10-18
categories: home-server
slug: 2022-10/process
---

## The Process

Backed up my previous Arch Linux based server for future reference.

1. Installed Debian as minimally as possible:
  - Used network install.
  - Set up encrypted LVM across entire drive.
  - Disabled `root` user. Created `owner` user for admin (`admin` wasn't allowed).
  - Installed no extra packages.
2. `sudo apt-get install vim git rsync`
3. Created users with `-m` (create home directory) and `-u` (custom uid) flags.
  - Changed `root` shell to `/bin/nologin`.
4. Created/copied luks encryption keys to `/root` and added entry in `/etc/crypttab`.
5. Added `tmpfs` and additional mount points to `/etc/fstab`
6. Configured `/etc/network/interfaces` for static IP. `sudo service networking restart`
7. Configured `/etc/systemd/logind.conf` - changed lid switch handling in all cases to `lock`. `sudo service systemd-logind restart`
8. Configured SSH - Set listen address. Disabled root login & password authentication. Added ClientAlive checks for a 5 minute inactivity timeout. `sudo service sshd restart`
9. Configured ZFS:
  - Installed using [OpenZFS docs](https://openzfs.github.io/openzfs-docs/Getting%20Started/Debian/).
  - 
10. Configured Samba shares for Windows and other devices. In Linux I generally use `sshfs`.
11. Set up backup and maintenance scripts.

Started version controlling config files. Will add files from the previous server backup to the commit history. Will be interesting to see how the configurations change in the coming years.
