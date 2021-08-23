# Working with Pods

## Creating Standalone Pods

To create standalone Pods a user with the required permissions can perform a single kubectl command. 
For example:

```bash
kubectl run [POD_NAME] --image=[IMAGE_NAME]:[IMAGE_TAG]
```

If we wanted to create a single NGINX Pod exposing the port 80/TCP of the container, the following command would work:

```bash
kubectl run nginx-example --image=nginx:latest --port=80    
```

Always remember that if you want to just create the specification of the pod, and not create the pod immediately, the flags `--dry-run=client -o yaml`, can be used to send the spec file to the stdout:

```bash
kubectl run nginx-example --image=nginx:latest --port=80 --dry-run=client -o yaml
    apiVersion: v1
    kind: Pod
    metadata:
    creationTimestamp: null
    labels:
        run: nginx-example
    name: nginx-example
    spec:
    containers:
    - image: nginx:latest
        name: nginx-example
        ports:
        - containerPort: 80
        resources: {}
    dnsPolicy: ClusterFirst
    restartPolicy: Always
    status: {}
```

## Working with existing Pods

### Deleting Pods

If desired to delete Pods that are already deployed inside the kubernetes cluster, we just need to grab the Pod name and then send a `kubectl delete pod [NAME]` command. 

```bash
kubectl get pod
    NAME           READY   STATUS    RESTARTS   AGE
    deleteme-pod   1/1     Running   0          6s

kubectl delete pod deleteme-pod
    pod "deleteme-pod" deleted
```

### Getting Pods Information

You can grab all the pods running on a specific namespace by passing the flag "`-n` or `--namespace`" on the `kubectl get pods` command:

```bash
kubectl get pods -n kube-system
    NAME                                     READY   STATUS    RESTARTS   AGE
    coredns-558bd4d5db-kp4k5                 1/1     Running   16         47d
    coredns-558bd4d5db-th5h4                 1/1     Running   16         47d
    etcd-k8stests.lab                        1/1     Running   6          6d4h
    kube-apiserver-k8stests.lab              1/1     Running   16         47d
    kube-controller-manager-k8stests.lab     1/1     Running   18         47d
    kube-proxy-wwfvr                         1/1     Running   16         47d
    kube-scheduler-k8stests.lab              1/1     Running   17         47d
```

You can get either a organized description of a specific Pod using the `kubectl describe` command or even get the currently applied specification file of a specific pod using the `kubectl get pod [NAME] -o yaml` command:

```bash
kubectl get pod
    NAME    READY   STATUS    RESTARTS   AGE
    myapp   1/1     Running   0          6s


kubectl describe pod myapp
    Name:         myapp
    Namespace:    default
    Priority:     0
    Node:         k8stests.lab/192.168.122.X
    Start Time:   Mon, 23 Aug 2021 14:56:52 +0100
    Labels:       run=myapp
    Annotations:  cni.projectcalico.org/podIP: 10.10.142.49/32
                cni.projectcalico.org/podIPs: 10.10.142.49/32
    Status:       Running
    IP:           10.10.142.49
    IPs:
    IP:  10.10.142.49
    Containers:
    myapp:
        Container ID:   containerd://XXX
        Image:          nginx:latest
        Image ID:       docker.io/library/nginx@sha256:XXX
        Port:           <none>
        Host Port:      <none>
        State:          Running
        Started:      Mon, 23 Aug 2021 14:56:55 +0100
        Ready:          True
        Restart Count:  0
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
    Type    Reason     Age   From               Message
    ----    ------     ----  ----               -------
    Normal  Scheduled  30s   default-scheduler  Successfully assigned default/myapp to k8stests.dfvaz
    Normal  Pulling    29s   kubelet            Pulling image "nginx:latest"
    Normal  Pulled     27s   kubelet            Successfully pulled image "nginx:latest" in 2.174677641s
    Normal  Created    27s   kubelet            Created container myapp
    Normal  Started    27s   kubelet            Started container myapp


kubectl get pod myapp -o yaml
    apiVersion: v1
    kind: Pod
    metadata:
    annotations:
        cni.projectcalico.org/podIP: 10.10.142.49/32
        cni.projectcalico.org/podIPs: 10.10.142.49/32
    creationTimestamp: "2021-08-23T13:56:52Z"
    labels:
        run: myapp
    name: myapp
    namespace: default
    resourceVersion: "131570"
    uid: XXX
    spec:
    containers:
    - image: nginx:latest
        imagePullPolicy: Always
        name: myapp
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
        volumeMounts:
        - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
        name: kube-api-access-947xq
        readOnly: true
    dnsPolicy: ClusterFirst
    enableServiceLinks: true
    nodeName: k8stests.lab
    preemptionPolicy: PreemptLowerPriority
    priority: 0
    restartPolicy: Always
    schedulerName: default-scheduler
    securityContext: {}
    serviceAccount: default
    serviceAccountName: default
    terminationGracePeriodSeconds: 30
    tolerations:
    - effect: NoExecute
        key: node.kubernetes.io/not-ready
        operator: Exists
        tolerationSeconds: 300
    - effect: NoExecute
        key: node.kubernetes.io/unreachable
        operator: Exists
        tolerationSeconds: 300
    volumes:
    - name: kube-api-access-947xq
        projected:
        defaultMode: 420
        sources:
        - serviceAccountToken:
            expirationSeconds: 3607
            path: token
        - configMap:
            items:
            - key: ca.crt
                path: ca.crt
            name: kube-root-ca.crt
        - downwardAPI:
            items:
            - fieldRef:
                apiVersion: v1
                fieldPath: metadata.namespace
                path: namespace
    status:
    conditions:
    - lastProbeTime: null
        lastTransitionTime: "2021-08-23T13:56:52Z"
        status: "True"
        type: Initialized
    - lastProbeTime: null
        lastTransitionTime: "2021-08-23T13:56:55Z"
        status: "True"
        type: Ready
    - lastProbeTime: null
        lastTransitionTime: "2021-08-23T13:56:55Z"
        status: "True"
        type: ContainersReady
    - lastProbeTime: null
        lastTransitionTime: "2021-08-23T13:56:52Z"
        status: "True"
        type: PodScheduled
    containerStatuses:
    - containerID: containerd://XXX
        image: docker.io/library/nginx:latest
        imageID: docker.io/library/nginx@sha256:XXX
        lastState: {}
        name: myapp
        ready: true
        restartCount: 0
        started: true
        state:
        running:
            startedAt: "2021-08-23T13:56:55Z"
    hostIP: 192.168.122.X
    phase: Running
    podIP: 10.10.142.49
    podIPs:
    - ip: 10.10.142.49
    qosClass: BestEffort
    startTime: "2021-08-23T13:56:52Z"
```

If a certain Pod is running and you wish to see what it is writing to stdout and stderr, you can use the `kubectl logs` command:

```bash
kubectl get pod
    NAME    READY   STATUS    RESTARTS   AGE
    myapp   1/1     Running   0          6s

kubectl logs myapp
    /docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
    /docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
    /docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
    10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
    10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
    /docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
    /docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
    /docker-entrypoint.sh: Configuration complete; ready for start up
```

### Remote-Shell into Pods

If you need to execute certain commands inside a running Pod, to troubleshoot any desired aspect the command `kubectl exec` can be used:

```bash
kubectl get pod
    NAME    READY   STATUS    RESTARTS   AGE
    myapp   1/1     Running   0          6s

kubectl exec -it myapp -- bash
    root@myapp:/#
```

Using the `exec -it` option we can run commands in interactive mode, allowing us to get a shell into the pod, and we can run as many commands as we like. If you just ant to execute a single command, the flags `-it` are not required, and the command can be specified after the `--` dashes, like so:

```bash
kubectl exec myapp -- cat /etc/os-release
    PRETTY_NAME="Debian GNU/Linux 10 (buster)"
    NAME="Debian GNU/Linux"
    VERSION_ID="10"
    VERSION="10 (buster)"
    VERSION_CODENAME=buster
    ID=debian
    HOME_URL="https://www.debian.org/"
    SUPPORT_URL="https://www.debian.org/support"
    BUG_REPORT_URL="https://bugs.debian.org/"
```
