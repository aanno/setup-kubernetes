# SLES 12 SP2 for kubernetes

## Install SLES 12 SP2

1. You (normally) only need the first DVD, i.e. 
   `SLE-12-SP2-Server-DVD-x86_64-GM-DVD1.iso`.
2. Download the iso from https://www.suse.com/de-de/download-linux/. The download
   is free of charge (for evaluation), but you need to create an account.
3. On install,
   + disable AppArmour
   + enable ssh login/port
   + disable the firewall
   + use a static IP address
   + don't forget to configure your default gateway
   + don't use swap, i.e. you don't need a swap partion (Swap is not supported
     by kubernetes!)
   + you probably want to set an account and the root password
   + if installing on a VM, only use _one_ partition (no home partition)
   + btrfs is probably a good choice (but untested with kubernetes/docker, hence
     you will get some warnings when you proceed)
  
## Test and complete networking and internet connection

1. Ensure that your network and internet connection is working.
2. You can modify network settings (gateway, ip address, gateway) with yast2.
3. If you change the hostname, don't forget to change it at /etc/hostname 
   as well (yast2 does not do that for you!).
  
## SLES registration (optional)

1. As developer, you could register the SLES instance for free (for evaluation). 
   Just create an account at 
   https://www.suse.com/de-de/subscriptions/sles/developer/ .
2. You can manage your registrations at the Suse Customer Center at 
   https://scc.suse.com/.
3. For registration, use yast2.
4. After registration, you could configure automatic updates in yast2.
5. After registration, you could update your installation with `zypper update`.
6. After registration, you could add the 'Container Module' (aka repository). 
   This way, you will be able to install the official (aka supported) docker
   version.

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
   + An very imported part of the docker setup is the 'docker storage driver'.
     The guide shows alternatives to using btrfs.
7. Enable the docker daemon with `systemctl start docker` and 
   `systemctl enable docker`.
8. To test if docker is working, you could use `systemctl status docker` and 
   `docker info`.
   
## Install some kubernetes packages

AFAIK, there are _no_ official kubernetes packages with SLES 12, as Suse tries 
to sell its CAAS platform (https://www.suse.com/de-de/products/caas-platform/) 
on this use case.

1. We use the packages from the Google Cloud Platform for (Redhat) EL7 
   (!). Details at https://github.com/GoogleCloudPlatform/compute-image-packages
2. Install package keys:
   ```
   wget https://packages.cloud.google.com/yum/doc/yum-key.gpg
   wget https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
   rpm --import rpm-package-key.gpg
   rpm --import yum-key.gpg
   ```
3. Add repository URL `https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64` 
   with yast2.
4. Add the packages `kubeadm`, `kubectl`, `kubelet`, and `kubernetes-cni` with yast2 
   or zypper.
   
## Follow the cluster setup at `doc/cluster-with-kubeadm.md`

The last steps for setting up a kubernetes cluster are independent of the linux 
distribution and documented at `doc/cluster-with-kubeadm.md`.

# Appendix

## Left-offs and not tried

* https://software.opensuse.org/download.html?project=Virtualization%3Acontainers&package=kubernetes
  points you to an yum receipte that includes the OpenSuse Repo mentioned.

   
