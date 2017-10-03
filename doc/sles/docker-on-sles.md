# Docker on SLES

## Install docker

1. For installing docker, you could use the 'Container Module' (see above).
2. As an alternative, you could use the OpenSuse Repo at
   http://download.opensuse.org/repositories/Virtualization:/containers/SLE_12_SP2
3. If you have trouble with package signing validation, you could find out the 
   URL of the public key in the *.repo file at the given location, download it 
   and import it with `rpm --import <gpg-pub-file>`.
4. Install docker with yast2 or `zypper install docker`.
5. The OpenSuse Repo also contains kubernetes-* packages, but we are _not_ 
   using this packages in our setup.
6. A detailed docker installation guide for Suse is at 
   https://www.suse.com/documentation/sles-12/singlehtml/book_sles_docker/book_sles_docker.html
   + An very important part of the docker setup is the 'docker storage driver'.
     The guide shows alternatives to using btrfs.
7. Enable the docker daemon with `systemctl start docker` and 
   `systemctl enable docker`.
8. To test if docker is working, you could use `systemctl status docker` and 
   `docker info`.
   
