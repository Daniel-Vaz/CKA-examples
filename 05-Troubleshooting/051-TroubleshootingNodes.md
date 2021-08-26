# Troubleshooting Cluster Nodes

```bash
# Grab Cluster Nodes
kubectl get nodes
    NAME             STATUS   ROLES                  AGE   VERSION
    k8stests.lab   Ready    control-plane,master   50d   v1.21.2

# Describe a Specific cluster Node
kubectl describe node k8stests.lab
    Name:               k8stests.lab
    Roles:              control-plane,master
    Labels:             beta.kubernetes.io/arch=amd64
                        beta.kubernetes.io/os=linux
                        kubernetes.io/arch=amd64
                        kubernetes.io/hostname=k8stests.lab
                        kubernetes.io/os=linux
                        node-role.kubernetes.io/control-plane=
                        node-role.kubernetes.io/master=
                        node.kubernetes.io/exclude-from-external-load-balancers=
    Annotations:        kubeadm.alpha.kubernetes.io/cri-socket: /run/containerd/containerd.sock
                        node.alpha.kubernetes.io/ttl: 0
                        projectcalico.org/IPv4Address: 192.168.122.X/24
                        projectcalico.org/IPv4VXLANTunnelAddr: 10.10.142.0
                        volumes.kubernetes.io/controller-managed-attach-detach: true
    CreationTimestamp:  Wed, 07 Jul 2021 09:19:27 +0100
    Taints:             <none>
    Unschedulable:      false
    Lease:
    HolderIdentity:  k8stests.lab
    AcquireTime:     <unset>
    RenewTime:       Thu, 26 Aug 2021 09:49:55 +0100
    Conditions:
    Type                 Status  LastHeartbeatTime                 LastTransitionTime                Reason                       Message
    ----                 ------  -----------------                 ------------------                ------                       -------
    NetworkUnavailable   False   Thu, 26 Aug 2021 09:37:43 +0100   Thu, 26 Aug 2021 09:37:43 +0100   CalicoIsUp                   Calico is running on this node
    MemoryPressure       False   Thu, 26 Aug 2021 09:47:41 +0100   Wed, 07 Jul 2021 10:04:50 +0100   KubeletHasSufficientMemory   kubelet has sufficient memory available
    DiskPressure         False   Thu, 26 Aug 2021 09:47:41 +0100   Wed, 07 Jul 2021 10:04:50 +0100   KubeletHasNoDiskPressure     kubelet has no disk pressure
    PIDPressure          False   Thu, 26 Aug 2021 09:47:41 +0100   Wed, 07 Jul 2021 10:04:50 +0100   KubeletHasSufficientPID      kubelet has sufficient PID available
    Ready                True    Thu, 26 Aug 2021 09:47:41 +0100   Tue, 24 Aug 2021 09:09:17 +0100   KubeletReady                 kubelet is posting ready status
    Addresses:
    InternalIP:  192.168.122.X
    Hostname:    k8stests.lab
    Capacity:
    cpu:                2
    ephemeral-storage:  17394Mi
    hugepages-1Gi:      0
    hugepages-2Mi:      0
    memory:             3826324Ki
    pods:               110
    Allocatable:
    cpu:                2
    ephemeral-storage:  16415037823
    hugepages-1Gi:      0
    hugepages-2Mi:      0
    memory:             3723924Ki
    pods:               110
    System Info:
    Machine ID:                 XXX
    System UUID:                XXX
    Boot ID:                    XXX
    Kernel Version:             4.18.0-305.7.1.el8_4.x86_64
    OS Image:                   CentOS Linux 8
    Operating System:           linux
    Architecture:               amd64
    Container Runtime Version:  containerd://1.4.6
    Kubelet Version:            v1.21.2
    Kube-Proxy Version:         v1.21.2
    PodCIDR:                      10.10.0.0/24
    PodCIDRs:                     10.10.0.0/24
    Non-terminated Pods:          (14 in total)
    Namespace                   Name                                        CPU Requests  CPU Limits  Memory Requests  Memory Limits  Age
    ---------                   ----                                        ------------  ----------  ---------------  -------------  ---
    calico-system               calico-kube-controllers-7f58dbcbbd-4fzqs    0 (0%)        0 (0%)      0 (0%)           0 (0%)         50d
    calico-system               calico-node-d7btg                           0 (0%)        0 (0%)      0 (0%)           0 (0%)         50d
    calico-system               calico-typha-6d8df84b4d-kkgcp               0 (0%)        0 (0%)      0 (0%)           0 (0%)         50d
    default                     myapp                                       0 (0%)        0 (0%)      0 (0%)           0 (0%)         2d18h
    default                     mydeploy-666dfd4f58-n5d4r                   0 (0%)        0 (0%)      0 (0%)           0 (0%)         2d
    default                     mydeploy-666dfd4f58-rq5b8                   0 (0%)        0 (0%)      0 (0%)           0 (0%)         2d
    kube-system                 coredns-558bd4d5db-kp4k5                    100m (5%)     0 (0%)      70Mi (1%)        170Mi (4%)     50d
    kube-system                 coredns-558bd4d5db-th5h4                    100m (5%)     0 (0%)      70Mi (1%)        170Mi (4%)     50d
    kube-system                 etcd-k8stests.lab                         100m (5%)     0 (0%)      100Mi (2%)       0 (0%)         8d
    kube-system                 kube-apiserver-k8stests.lab               250m (12%)    0 (0%)      0 (0%)           0 (0%)         50d
    kube-system                 kube-controller-manager-k8stests.lab      200m (10%)    0 (0%)      0 (0%)           0 (0%)         50d
    kube-system                 kube-proxy-wwfvr                            0 (0%)        0 (0%)      0 (0%)           0 (0%)         50d
    kube-system                 kube-scheduler-k8stests.lab               100m (5%)     0 (0%)      0 (0%)           0 (0%)         50d
    tigera-operator             tigera-operator-86c4fc874f-r4jxz            0 (0%)        0 (0%)      0 (0%)           0 (0%)         50d
    Allocated resources:
    (Total limits may be over 100 percent, i.e., overcommitted.)
    Resource           Requests    Limits
    --------           --------    ------
    cpu                850m (42%)  0 (0%)
    memory             240Mi (6%)  340Mi (9%)
    ephemeral-storage  0 (0%)      0 (0%)
    hugepages-1Gi      0 (0%)      0 (0%)
    hugepages-2Mi      0 (0%)      0 (0%)
    Events:
    Type     Reason                   Age                From        Message
    ----     ------                   ----               ----        -------
    Normal   Starting                 17h                kubelet     Starting kubelet.
    Warning  InvalidDiskCapacity      17h                kubelet     invalid capacity 0 on image filesystem
    Normal   NodeHasSufficientMemory  17h (x8 over 17h)  kubelet     Node k8stests.lab status is now: NodeHasSufficientMemory
    Normal   NodeHasNoDiskPressure    17h (x7 over 17h)  kubelet     Node k8stests.lab status is now: NodeHasNoDiskPressure
    Normal   NodeHasSufficientPID     17h (x7 over 17h)  kubelet     Node k8stests.lb status is now: NodeHasSufficientPID
    Normal   NodeAllocatableEnforced  17h                kubelet     Updated Node Allocatable limit across pods
    Normal   Starting                 17h                kube-proxy  Starting kube-proxy.
    Normal   Starting                 12m                kubelet     Starting kubelet.
    Warning  InvalidDiskCapacity      12m                kubelet     invalid capacity 0 on image filesystem
    Normal   NodeAllocatableEnforced  12m                kubelet     Updated Node Allocatable limit across pods
    Normal   NodeHasNoDiskPressure    12m (x7 over 12m)  kubelet     Node k8stests.lab status is now: NodeHasNoDiskPressure
    Normal   NodeHasSufficientPID     12m (x7 over 12m)  kubelet     Node k8stests.lab status is now: NodeHasSufficientPID
    Normal   NodeHasSufficientMemory  12m (x8 over 12m)  kubelet     Node k8stests.lab status is now: NodeHasSufficientMemory
    Normal   Starting                 12m                kube-proxy  Starting kube-proxy.

# Get Cluster Events
kubectl get events
    LAST SEEN   TYPE      REASON                    OBJECT                                 MESSAGE
    17h         Normal    Starting                  node/k8stests.lab                      Starting kubelet.
    17h         Warning   InvalidDiskCapacity       node/k8stests.lab                      invalid capacity 0 on image filesystem
    17h         Normal    NodeHasSufficientMemory   node/k8stests.lab                      Node k8stests.lab status is now: NodeHasSufficientMemory
    17h         Normal    NodeHasNoDiskPressure     node/k8stests.lab                      Node k8stests.lab status is now: NodeHasNoDiskPressure
    17h         Normal    NodeHasSufficientPID      node/k8stests.lab                      Node k8stests.lab status is now: NodeHasSufficientPID
    17h         Normal    NodeAllocatableEnforced   node/k8stests.lab                      Updated Node Allocatable limit across pods
    17h         Normal    Starting                  node/k8stests.lab                      Starting kube-proxy.
    17h         Normal    RegisteredNode            node/k8stests.lab                      Node k8stests.lab event: Registered Node k8stests.lab in Controller
    14m         Normal    Starting                  node/k8stests.lab                      Starting kubelet.
    14m         Warning   InvalidDiskCapacity       node/k8stests.lab                      invalid capacity 0 on image filesystem
    14m         Normal    NodeHasSufficientMemory   node/k8stests.lab                      Node k8stests.lab status is now: NodeHasSufficientMemory
    14m         Normal    NodeHasNoDiskPressure     node/k8stests.lab                      Node k8stests.lab status is now: NodeHasNoDiskPressure
    14m         Normal    NodeHasSufficientPID      node/k8stests.lab                      Node k8stests.lab status is now: NodeHasSufficientPID
    14m         Normal    NodeAllocatableEnforced   node/k8stests.lab                      Updated Node Allocatable limit across pods

# Dump all Cluster Information
kubectl cluster-info dump
```

## Logs

For **Master Nodes** the logs files are located at:

- `/var/log/kube-apiserver.log` - API Server, responsible for serving the API

- `/var/log/kube-scheduler.log` - Scheduler, responsible for making scheduling decisions

- `/var/log/kube-controller-manager.log` - Controller that manages replication controllers

For **Worker Nodes** the logs files are located at:

- `/var/log/kubelet.log` - Kubelet, responsible for running containers on the node

- `/var/log/kube-proxy.log` - Kube Proxy, responsible for service load balancing
