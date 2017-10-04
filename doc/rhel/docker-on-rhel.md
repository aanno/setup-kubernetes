# Docker on RHEL and Fedora

## RHEL

* https://stackoverflow.com/questions/45272827/docker-ce-on-rhel-requires-container-selinux-2-9
* https://github.com/docker/for-linux/issues/20

### Docker and firewallD

* https://unix.stackexchange.com/questions/199966/how-to-configure-centos-7-firewalld-to-allow-docker-containers-free-access-to-th
* https://sanenthusiast.com/docker-and-firewalld-mess-in-centos-7/ (less helpful)

## Fedora

### Docker and firewallD

*The* biggest obstacle to run docker on fedora is firewallD!

* https://opsech.io/posts/2017/May/23/docker-dns-with-firewalld-on-fedora.html
* https://superuser.com/questions/1180870/fedora-firewalld-issues-with-docker
* https://github.com/moby/moby/issues/16137#issuecomment-271615192
* https://github.com/firewalld/firewalld/issues/195
