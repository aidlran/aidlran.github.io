---
layout: post
title: Home Server October 2022 Update Background
date: 2022-10-18
categories: home-server
slug: 2022-10/background
---

<!-- TODO: add/verify links -->

I have decided to log updates to my home network/server setup. It's really nothing fancy as of now - it's literally just an old laptop hooked up to a hard drive enclosure.

## Background

This is the first I've written about my home network, so here's the background:

I use two machines - a Linux laptop for daily use, and a Windows desktop for Windows-specific activities that I cannot feasibly use a VM for, including gaming and music production.

> As an aside, I know all about KVM and GPU passthrough etc. and believe me I've tried it, but VMs on the cheap hardware I currently have are just not stable and performant enough for me, and let's not even get into the DRM issues!

In early 2022, I was using a 2TB hard drive in an enclosure for my data hoarding needs. It was previously in the Windows machine, so it was still formatted as NTFS, but I could hook it up to a machine when I needed it and it was fine.

I eventually upgraded to a 4TB NAS grade hard drive, however I wanted to change to a BTRFS file system for its compression and use LUKS encryption. Unfortunately, Windows does not natively support either of these. I looked for solutions and even experimented with WSL (Windows subsystem for Linux) but it was clear this would not be very practical.

That's when I got the bright idea to hook up this hard drive to an old laptop and set it up as a NAS device. I would use `sshfs` for Linux, and set up Samba shares for Windows. I set this up using Arch Linux with the LTS kernel. Why use Arch for a server? Well, I was already familiar with it, and I wanted something minimal that I could set up quickly and have a high level of contol over.

I didn't want to use a dedicated NAS OS as I figured that, as a developer, I would want to use the server for other applications.

## The Problem

The problem with this is that my data was not secure! Sure, it was encrypted now, so it was secure in that sense, but there was still no redundancy. If the hard drive had failed then I could have lost everything. I was constantly paranoid about this fact.

Luckily, [Proton](https://proton.me) had recently upgraded their plans and given me a huge ~500GB of cloud storage to take advantage of, but I can still only back up the most important stuff with this space, and I would be bottlenecked by consumer internet speeds. Another downside is that their Proton Drive service is relatively new at the time of writing and still does not offer a public API that I could use to automate things.

What I really needed was a mirrored or RAID storage solution. Unfortunately cost was a barrier for a long while as this would require multiple drives and some sort of enclosure to keep them safe.

## Upgrades!

This month, I finally got around to purchasing an enclosure and two brand-new 4TB NAS drives, bringing my total to three - a modest 12TB when combined.

I have set up a ZFS mirrored pool on the two new drives. Each drive is a mirror of the other. I only have 4TB of space available to use in the pool out of the 8TB available on the combined disks, however if one of the drives fail then the data is not lost, and hopefully the failed drive can be replaced before the other fails too. ZFS even has support for compression and encryption built in.

I also decided to keep the existing drive with its LUKS encrypted BTRFS file system. My plan is to gradually begin transferring data over to the ZFS pool. I want to do it right and have a logical directory structure, accurate metadata, and correct permissions (I'm careful like that, plus I sometimes allow access to the server for other users.) This drive will be used to regularly sync important data from the ZFS pool, and the extra space will be used as an overflow of sorts in case the ZFS pool gets full.

## Goodbye, Arch Linux

Getting ZFS working on Linux (Arch in particular) can be somewhat complicated. ZFS cannot be included in the kernel like BTRFS is due to licensing incompatibilities, and the ZFS version you install is dependant on a specific kernel version, which can make updating a nightmare. I ultimately decided to switch my server's OS from Arch Linux over to Debian simply to make the ZFS installation process easier and to reduce the likelihood of future headaches. I'll continue to daily drive Arch on my workstation, though.

It took a day to re-install and re-configure everything, but the fresh start was definitely needed. I was able to set things up properly from the get-go thanks to all that I have learned since I started this project. I basically feel that the system is slightly less reminiscent of cooked spaghetti now.

For instance: I've been documenting and writing backup and maintenance scripts as I go. I'm even automatically version controlling any config files! Everything is automated by `crontab`, except pushing to origin - I do that manually from my workstation as I don't want those SSH keys on the server.

## Redundancy Strategy

It's important to follow the well-established 3-2-1 rule of redundancy when it comes to backups. That is:

- At least **3** copies of the data.
- Copies on at least **2** different mediums.
- At least **1** copy off-site.

My most important data has redundancy that satisfies me for now. I now have two copies on-site (arguably three if you count the ZFS mirroring,) as well as at least another copy off-site in cloud storage.

> Slightly off-topic but allow me to preach about privacy once again... at this time I'm using Proton Drive which is end-to-end encrypted as one of its primary selling points, however I *still* use `gpg -c --cipher-algo aes256` to encrypt the archive files that I upload. I ***strongly*** recommend encrypting your data before sending it off to cloud storage!

Some of the data is only on the ZFS mirror, but that's okay. We're talking about data that would be a bummer to lose, but that I could likely reacquire - things like music, films, software, and resources for development and music production.

That said, I do hope to expand the mirror - or implement ZRAID2 or greater - such that I have more storage and tolerance for two (or more) failed drives, but that will require many more hard drives (expensive.) I am also considering services like Amazon Glacier for long term storage of terrabytes of data (also expensive.)

## State of Play

Behold my awesome server (an old laptop with several hard drives hooked up to it):

<!-- TODO: more specific -->
- Intel Pentium CPU (4c, 4t)
- 8GB memory
- Whatever network interface the laptop has - probably 1 gigabit?
- 128GB NVME SSD - *boot/OS drive*
- 2 x 4TB Seagate IronWolf NAS HDD - *mirrored ZFS pool*
- 4TB Seagate IronWolf NAS HDD - *backup/overflow*

<!-- TODO: add a neofetch of the server -->

With more space and cash, I would absolutely love to put together a proper server build in a Fractal Node 808 case <!-- TODO: double check case name? --> (8 drive bays!) and use ECC memory and everything.

With more server resources I might even try out Proxmox VE again and play around with distributed virtual machines. That will be great as I start to build and test more full-stack applications and databases, but I can't help but wonder if I could eventually ditch the Windows PC in favour of a VM?

Alas, I'll have to make do with my poor old laptop for now.
