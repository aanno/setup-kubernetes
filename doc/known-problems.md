# Known problems

## Pod network

There are a couple of alternatives mentioned at 
https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/
. However, I've no clue how they differ.

## btrfs as docker storage driver

As mentioned at https://github.com/Mirantis/kubeadm-dind-cluster
btrfs should be avoided because of a 
[docker bug](https://github.com/docker/docker/issues/9939) 
_and_ a 
[kubernetes bug](https://github.com/kubernetes/kubernetes/issues/38337).

## Does `kubeadm` setup a production-grade cluster?

* Is there a way to migrate the essential services of the cluseter
  setup with `kubeadm` to bare-metal?
* Is salt a better alternative for setup of a production cluster?
* How to archive HA?

## Offline install

* `kubeadm` uses online images of essential services. Could this be 
  circumvented?
  
