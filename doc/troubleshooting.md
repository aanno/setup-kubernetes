# Important monitoring commands

```
kubelet

journalctl -xeu kubelet
journalctl -u kubelet -f


less /var/log/messages

kubectl get nodes
kubectl get pods --all-namespaces
```

# Problems and Solutions

## Problem: `kubeadm` hangs at: [apiclient] Created API client

* `kubeadm` hangs at: 
  ```
  [apiclient] Created API client, waiting for the control plane to become ready
  ```
* In `/var/log/messages`:
  ```
  2017-09-27T09:36:49.339251+02:00 kube1-master kubelet[9675]: error: failed to run Kubelet: failed to create kubelet: misconfiguration: kubelet cgroup driver: "systemd" is different from docker cgroup driver: "cgroupfs"
  ```

### Solution

#### Solution 1

Suse: 
```
gedit /etc/systemd/system/kubelet.service.d/10-kubeadm.conf 
```

Comment out indicated line:
```
# Environment="KUBELET_CGROUP_ARGS=--cgroup-driver=systemd"
Environment="KUBELET_CGROUP_ARGS=--cgroup-driver=cgroupfs"
```
Source: https://github.com/kubernetes/kubeadm/issues/228

#### Solution 2

Docker startup mod:
```
ExecStart=/usr/bin/dockerd --exec-opt native.cgroupdriver=systemd
```

## Problem: swap is not supported

kubelet logs:
```
W0927 10:56:15.917471   10611 container_manager_linux.go:218] Running with swap on is not supported, please disable swap! This will be a fatal error by default starting in K8s v1.6! In the meantime, you can opt-in to making this a fatal error by enabling --experimental-fail-swap-on.
```

### Solution

Don't use swap. Disable it.

## Problem: docker warnings

```
docker info 
```

Leads to: 

```
[...]
WARNING: No swap limit support
WARNING: No kernel memory limit support
[...]
```

## Problem: Remove all docker images and containers

You use Docker, but working with it created lots of images and containers. You want to remove all of them to save disk space.
Solution:

Warning: This will destroy all your images and containers. It will not be possible to restore them!
Run those commands in a shell:

### Solution
```
#!/bin/bash
# Delete all containers
docker rm $(docker ps -a -q)
# Delete all images
docker rmi $(docker images -q)
```

Source: https://techoverflow.net/2013/10/22/docker-remove-all-images-and-containers/

## Problem: no tree command on SLES

### Solution

Add the following repository:
https://download.opensuse.org/repositories/home:/Alexander_Naumov:/SLE12/SLE_12_GA/
