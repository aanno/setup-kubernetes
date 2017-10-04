# Docker installation

Docker is a delicate beast for installing.

## Different docker version

While most distributions currently ship an docker 1.12.x version, 
the company behind docker has split the software into 2 versions: 
docker-ce (community edition) and docker-ee (enterprise edition).
The former is open source, the later is a commercial product.

The most recent version of docker-ce is 17.09.0.

## Different storage driver

If you use docker for anything apart for playing around for half-an-hour
you *need* an _storage driver_. You normally want the device mapper
(direct-lvm) driver, but see below.

### btrfs

<div class="alert alert-success">
Using the docker btrfs driver is *_very_ dangerous*. See below.
</div><br/>

Quote from https://www.suse.com/documentation/sles-12/singlehtml/book_sles_docker/book_sles_docker.html:

> It is recommended to have `/var/lib/docker` mounted on a separate partition 
or volume to not affect the Docker host operating system in case of a file 
system corruption.
>
> In case you choose the Btrfs file system for `/var/lib/docker`, it is strongly 
recommended to create a *subvolume* for it. This ensures that the directory is 
excluded from file system snapshots. If not excluding `/var/lib/docker` from 
snapshots, the file system will likely run out of disk space soon after 
you start deploying containers. What's more, a rollback to a previous snapshot 
will also reset the Docker database and images. Disabling copy-on-write for 
this subvolume is also recommended, to avoid duplication of data blocks. 
Refer to Creating and Mounting New Subvolumes at 
https://www.suse.com/documentation/sles-12/book_sle_admin/data/sec_snapper_setup.html 
for details. 

### device mapper

Device mapper (direct-lvm) driver is recommended. Practically, this means
that you use docker on an LVM2 installed linux _with a separate logical 
volume dedicated for docker images_.

* https://docs.docker.com/engine/userguide/storagedriver/device-mapper-driver/#configure-direct-lvm-mode-for-production

### Unresolved: Think about `xfs`

As docker device mapper uses `xfs` for its data, perhaps using `xfs` all-over
an additional benefit. 

(More infos about filesystems: https://www.suse.com/documentation/sles-12/singlehtml/stor_admin/stor_admin.html)

## Security with docker

* https://stackoverflow.com/questions/24319662/from-inside-of-a-docker-container-how-do-i-connect-to-the-localhost-of-the-mach
  Networking with `bridge` and `host`

### SELinux and firewall issues

* https://github.com/moby/moby/issues/16137#issuecomment-271615192
* https://github.com/firewalld/firewalld/issues/195

The issue is *unresolved*.

## ipv6

* https://www.puzzle.ch/blog/articles/2017/06/13/docker-container-mit-ipv6-anbinden
* https://docs.docker.com/engine/userguide/networking/default_network/ipv6/
