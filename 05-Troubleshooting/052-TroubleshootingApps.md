# Troubleshooting running Workloads

```bash
# Get Deployed Pods
kubectl get pods
    NAME                        READY   STATUS    RESTARTS   AGE
    myapp                       0/1     Unknown   4          2d19h
    mydeploy-666dfd4f58-n5d4r   1/1     Running   4          2d1h
    mydeploy-666dfd4f58-rq5b8   1/1     Running   4          2d1h

# Describe Pods
kubectl describe pod myapp
    Name:         myapp
    Namespace:    default
    Priority:     0
    Node:         k8stests.lab/192.168.122.X
    Start Time:   Mon, 23 Aug 2021 14:56:52 +0100
    Labels:       run=myapp
    Annotations:  cni.projectcalico.org/podIP: 10.10.142.24/32
                cni.projectcalico.org/podIPs: 10.10.142.24/32
    Status:       Running
    IP:           10.10.142.24
    IPs:
    IP:  10.10.142.24
    Containers:
    myapp:
        Container ID:   containerd://XXX
        Image:          nginx:latest
        Image ID:       docker.io/library/nginx@sha256:XXX
        Port:           <none>
        Host Port:      <none>
        State:          Running
        Started:      Thu, 26 Aug 2021 10:51:43 +0100
        Last State:     Terminated
        Reason:       Unknown
        Exit Code:    255
        Started:      Thu, 26 Aug 2021 09:38:29 +0100
        Finished:     Thu, 26 Aug 2021 10:50:33 +0100
        Ready:          True
        Restart Count:  5
        Environment:    <none>
        Mounts:
        /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-947xq (ro)
    Conditions:
    Type              Status
    Initialized       True
    Ready             True
    ContainersReady   True
    PodScheduled      True
    Volumes:
    kube-api-access-947xq:
        Type:                    Projected (a volume that contains injected data from multiple sources)
        TokenExpirationSeconds:  3607
        ConfigMapName:           kube-root-ca.crt
        ConfigMapOptional:       <nil>
        DownwardAPI:             true
    QoS Class:                   BestEffort
    Node-Selectors:              <none>
    Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                                node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
    Events:
    Type    Reason          Age                From     Message
    ----    ------          ----               ----     -------
    Normal  SandboxChanged  18h (x2 over 18h)  kubelet  Pod sandbox changed, it will be killed and re-created.
    Normal  Pulling         18h                kubelet  Pulling image "nginx:latest"
    Normal  Pulled          18h                kubelet  Successfully pulled image "nginx:latest" in 2.59638093s
    Normal  Created         18h                kubelet  Created container myapp
    Normal  Started         18h                kubelet  Started container myapp
    Normal  SandboxChanged  73m (x2 over 74m)  kubelet  Pod sandbox changed, it will be killed and re-created.
    Normal  Pulling         73m                kubelet  Pulling image "nginx:latest"
    Normal  Created         73m                kubelet  Created container myapp
    Normal  Pulled          73m                kubelet  Successfully pulled image "nginx:latest" in 1.714757523s
    Normal  Started         73m                kubelet  Started container myapp
    Normal  SandboxChanged  20s (x2 over 63s)  kubelet  Pod sandbox changed, it will be killed and re-created.
    Normal  Pulling         19s                kubelet  Pulling image "nginx:latest"
    Normal  Pulled          17s                kubelet  Successfully pulled image "nginx:latest" in 1.665048441s
    Normal  Created         17s                kubelet  Created container myapp
    Normal  Started         17s                kubelet  Started container myapp

# Get Pod Logs
kubectl logs myapp
    /docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
    /docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
    /docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
    10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf

# Get Previous iterations, Pods Logs
kubectl logs --previous myapp
    2021/08/26 09:23:18 [notice] 1#1: signal 17 (SIGCHLD) received from 32
    2021/08/26 09:23:18 [notice] 1#1: worker process 32 exited with code 0
    2021/08/26 09:23:18 [notice] 1#1: signal 29 (SIGIO) received
    2021/08/26 09:23:18 [notice] 1#1: signal 17 (SIGCHLD) received from 31
    2021/08/26 09:23:18 [notice] 1#1: worker process 31 exited with code 0
    2021/08/26 09:23:18 [notice] 1#1: exit

# Remote Shell inside Running Pod
kubectl exec -it myapp -- bash
    root@myapp:/#
```
