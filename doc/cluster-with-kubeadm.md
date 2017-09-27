# Cluster creation with `kubeadm`

`kubeadm` only needs an installed `kubelet` kubernetes package. All other 
essential kubernetes services are run in the clould itself (on the master node).

In a 'real' production environment, the essential kubernetes services should 
run on bare-metal (and shoud be clustered for HA). `kubeadm` can't set up such
an environment, however. 

## Setup the master node

```
kubeadm init --skip-preflight-checks --token-ttl 0
```

1. `--skip-preflight-checks` is needed because of btrfs. Leave the flag out 
   for a first check. More details at 
   https://github.com/kubernetes/kubeadm/issues/164
2. Some pod networks needs `--pod-network-cidr=10.244.0.0/16` or similiar.
3. More details at https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/

Output:
```
[kubeadm] WARNING: kubeadm is in beta, please do not use it for production clusters.
[init] Using Kubernetes version: v1.7.6
[init] Using Authorization modes: [Node RBAC]
[preflight] Skipping pre-flight checks
[kubeadm] WARNING: starting in 1.8, tokens expire after 24 hours by default (if you require a non-expiring token use --token-ttl 0)
[certificates] Generated CA certificate and key.
[certificates] Generated API server certificate and key.
[certificates] API Server serving cert is signed for DNS names [kube1-master kubernetes kubernetes.default kubernetes.default.svc kubernetes.default.svc.cluster.local] and IPs [10.96.0.1 192.168.1.220]
[certificates] Generated API server kubelet client certificate and key.
[certificates] Generated service account token signing key and public key.
[certificates] Generated front-proxy CA certificate and key.
[certificates] Generated front-proxy client certificate and key.
[certificates] Valid certificates and keys now exist in "/etc/kubernetes/pki"
[kubeconfig] Wrote KubeConfig file to disk: "/etc/kubernetes/admin.conf"
[kubeconfig] Wrote KubeConfig file to disk: "/etc/kubernetes/kubelet.conf"
[kubeconfig] Wrote KubeConfig file to disk: "/etc/kubernetes/controller-manager.conf"
[kubeconfig] Wrote KubeConfig file to disk: "/etc/kubernetes/scheduler.conf"
[apiclient] Created API client, waiting for the control plane to become ready
[apiclient] All control plane components are healthy after 4018.000738 seconds
[token] Using token: <token>
[apiconfig] Created RBAC rules
[addons] Applied essential addon: kube-proxy
[addons] Applied essential addon: kube-dns

Your Kubernetes master has initialized successfully!

To start using your cluster, you need to run (as a regular user):

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  http://kubernetes.io/docs/admin/addons/

You can now join any number of machines by running the following on each node
as root:

  kubeadm join --token <token> 192.168.1.220:6443
```

1. Obviously, it is recommended to follow the instructions printed out.

## Setup the worker nodes

```
kubeadm join --skip-preflight-checks --token <token> <master-ip-addr>:6443 
```

Output:
```
[kubeadm] WARNING: kubeadm is in beta, please do not use it for production clusters.
[preflight] Skipping pre-flight checks
[discovery] Trying to connect to API Server "192.168.1.220:6443"
[discovery] Created cluster-info discovery client, requesting info from "https://192.168.1.220:6443"
[discovery] Cluster info signature and contents are valid, will use API Server "https://192.168.1.220:6443"
[discovery] Successfully established connection with API Server "192.168.1.220:6443"
[bootstrap] Detected server version: v1.7.6
[bootstrap] The server supports the Certificates API (certificates.k8s.io/v1beta1)
[csr] Created API client to obtain unique certificate for this node, generating keys and certificate signing request
[csr] Received signed certificate from the API server, generating KubeConfig...
[kubeconfig] Wrote KubeConfig file to disk: "/etc/kubernetes/kubelet.conf"

Node join complete:
* Certificate signing request sent to master and response
  received.
* Kubelet informed of new secure connection details.

Run 'kubectl get nodes' on the master to see this machine join.
```

## Important status checking commands

## Cluster and/or master

```
kubectl get nodes
```

Output:
```
NAME           STATUS    AGE       VERSION
kube1-master   Ready     5h        v1.7.5
kube1-node1    Ready     2h        v1.7.5
kube1-node2    Ready     1h        v1.7.5
```

```
kubectl get pods --all-namespaces
```

Output:
```
NAMESPACE     NAME                                   READY     STATUS    RESTARTS   AGE
kube-system   etcd-kube1-master                      1/1       Running   2          4h
kube-system   kube-apiserver-kube1-master            1/1       Running   2          4h
kube-system   kube-controller-manager-kube1-master   1/1       Running   2          4h
kube-system   kube-dns-2425271678-k3r0m              3/3       Running   6          5h
kube-system   kube-proxy-hsc2z                       1/1       Running   0          2h
kube-system   kube-proxy-jltsk                       1/1       Running   2          5h
kube-system   kube-proxy-vq2x3                       1/1       Running   0          1h
kube-system   kube-scheduler-kube1-master            1/1       Running   2          4h
kube-system   weave-net-2htl3                        2/2       Running   0          2h
kube-system   weave-net-7s0dd                        2/2       Running   4          5h
kube-system   weave-net-s4xw9                        2/2       Running   0          1h
```

## All nodes

```
docker ps
```

Output:
```
CONTAINER ID        IMAGE                                                                                                                            COMMAND                  CREATED             STATUS              PORTS               NAMES
cdfbe1676f29        weaveworks/weave-npc@sha256:676b3a815e2cb2477e6d4ed3c6699c3d3a9c23e30b8f5e76af8e5c0b5030d525                                     "/usr/bin/weave-npc"     3 hours ago         Up 3 hours                              k8s_weave-npc_weave-net-7s0dd_kube-system_875f7534-a35a-11e7-9972-02d7c130505a_2
a70b90e6bf1b        gcr.io/google_containers/k8s-dns-sidecar-amd64@sha256:97074c951046e37d3cbb98b82ae85ed15704a290cce66a8314e7f846404edde9           "/sidecar --v=2 --log"   3 hours ago         Up 3 hours                              k8s_sidecar_kube-dns-2425271678-k3r0m_kube-system_33027225-a358-11e7-9972-02d7c130505a_2
a962a431b217        gcr.io/google_containers/k8s-dns-dnsmasq-nanny-amd64@sha256:aeeb994acbc505eabc7415187cd9edb38cbb5364dc1c2fc748154576464b3dc2     "/dnsmasq-nanny -v=2 "   3 hours ago         Up 3 hours                              k8s_dnsmasq_kube-dns-2425271678-k3r0m_kube-system_33027225-a358-11e7-9972-02d7c130505a_2
df3d51dbeca2        gcr.io/google_containers/k8s-dns-kube-dns-amd64@sha256:40790881bbe9ef4ae4ff7fe8b892498eecb7fe6dcc22661402f271e03f7de344          "/kube-dns --domain=c"   3 hours ago         Up 3 hours                              k8s_kubedns_kube-dns-2425271678-k3r0m_kube-system_33027225-a358-11e7-9972-02d7c130505a_2
f2f84042a0a0        gcr.io/google_containers/pause-amd64:3.0                                                                                         "/pause"                 3 hours ago         Up 3 hours                              k8s_POD_kube-dns-2425271678-k3r0m_kube-system_33027225-a358-11e7-9972-02d7c130505a_7
4bc252b25a9c        weaveworks/weave-kube@sha256:cddb2620620481271763d39c797b2f0b970d18bc5e98ffb7151a3c5f8a923324                                    "/home/weave/launch.s"   3 hours ago         Up 3 hours                              k8s_weave_weave-net-7s0dd_kube-system_875f7534-a35a-11e7-9972-02d7c130505a_2
d0b9b7e1f0e4        gcr.io/google_containers/kube-proxy-amd64@sha256:1509f2fc8a60501d604d21d983ed6f5d0ea40ccdd7cc6ba6c994389ef7db16d8                "/usr/local/bin/kube-"   3 hours ago         Up 3 hours                              k8s_kube-proxy_kube-proxy-jltsk_kube-system_32f86150-a358-11e7-9972-02d7c130505a_2
0546ea1b8cba        gcr.io/google_containers/pause-amd64:3.0                                                                                         "/pause"                 3 hours ago         Up 3 hours                              k8s_POD_weave-net-7s0dd_kube-system_875f7534-a35a-11e7-9972-02d7c130505a_2
dcb61b147859        gcr.io/google_containers/pause-amd64:3.0                                                                                         "/pause"                 3 hours ago         Up 3 hours                              k8s_POD_kube-proxy-jltsk_kube-system_32f86150-a358-11e7-9972-02d7c130505a_2
d9103e4f072a        gcr.io/google_containers/etcd-amd64@sha256:d83d3545e06fb035db8512e33bd44afb55dea007a3abd7b17742d3ac6d235940                      "etcd --listen-client"   3 hours ago         Up 3 hours                              k8s_etcd_etcd-kube1-master_kube-system_9ef6d25e21bb4befeabe4d0e4f72d1ca_2
07f2e7e2c699        gcr.io/google_containers/kube-apiserver-amd64@sha256:f3a208d30314a89952cf613e5ee671f9d2ed7b197cd6c5d91bebfe02571d7e1b            "kube-apiserver --adm"   3 hours ago         Up 3 hours                              k8s_kube-apiserver_kube-apiserver-kube1-master_kube-system_2e89d2230b8237ce2210c857fddca5a4_2
230da6570fe2        gcr.io/google_containers/kube-scheduler-amd64@sha256:334a38ac844be07599f74876f6c923271bbd0aab48a43e7ca1ad4942e9ebdabd            "kube-scheduler --add"   3 hours ago         Up 3 hours                              k8s_kube-scheduler_kube-scheduler-kube1-master_kube-system_6705be51ceb05190da8e37fa641165e0_2
acc0325e4300        gcr.io/google_containers/kube-controller-manager-amd64@sha256:42a42e8d39fd68de7c1db6844f909bfa6bff89019ecef86e6c542354cf8ab9fb   "kube-controller-mana"   3 hours ago         Up 3 hours                              k8s_kube-controller-manager_kube-controller-manager-kube1-master_kube-system_ed47de27675f4f1e9d47bc43e0260e1d_2
ddc573f0b3f7        gcr.io/google_containers/pause-amd64:3.0                                                                                         "/pause"                 3 hours ago         Up 3 hours                              k8s_POD_kube-scheduler-kube1-master_kube-system_6705be51ceb05190da8e37fa641165e0_2
93b78e220e6e        gcr.io/google_containers/pause-amd64:3.0                                                                                         "/pause"                 3 hours ago         Up 3 hours                              k8s_POD_kube-controller-manager-kube1-master_kube-system_ed47de27675f4f1e9d47bc43e0260e1d_2
459bb91bb084        gcr.io/google_containers/pause-amd64:3.0                                                                                         "/pause"                 3 hours ago         Up 3 hours                              k8s_POD_kube-apiserver-kube1-master_kube-system_2e89d2230b8237ce2210c857fddca5a4_2
7be06c039695        gcr.io/google_containers/pause-amd64:3.0                                                                                         "/pause"                 3 hours ago         Up 3 hours                              k8s_POD_etcd-kube1-master_kube-system_9ef6d25e21bb4befeabe4d0e4f72d1ca_2
```

```
docker images
```

Output:
```
REPOSITORY                                               TAG                 IMAGE ID            CREATED             SIZE
gcr.io/google_containers/kube-controller-manager-amd64   v1.7.6              028bd65dc783        13 days ago         138 MB
gcr.io/google_containers/kube-scheduler-amd64            v1.7.6              c3101592d24c        13 days ago         77.2 MB
gcr.io/google_containers/kube-apiserver-amd64            v1.7.6              f15a956e781d        13 days ago         186.1 MB
gcr.io/google_containers/kube-proxy-amd64                v1.7.6              af674cbf7039        13 days ago         114.7 MB
weaveworks/weave-npc                                     2.0.4               a33806b1a7de        4 weeks ago         54.69 MB
weaveworks/weave-kube                                    2.0.4               3e2e9472eefc        4 weeks ago         100.7 MB
gcr.io/google_containers/k8s-dns-sidecar-amd64           1.14.4              38bac66034a6        3 months ago        41.81 MB
gcr.io/google_containers/k8s-dns-kube-dns-amd64          1.14.4              a8e00546bcf3        3 months ago        49.38 MB
gcr.io/google_containers/k8s-dns-dnsmasq-nanny-amd64     1.14.4              f7f45b9cb733        3 months ago        41.41 MB
gcr.io/google_containers/etcd-amd64                      3.0.17              243830dae7dd        7 months ago        168.9 MB
gcr.io/google_containers/pause-amd64                     3.0                 99e59f495ffa        17 months ago       746.9 kB
```
