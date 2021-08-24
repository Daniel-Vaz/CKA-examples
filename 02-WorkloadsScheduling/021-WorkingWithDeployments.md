# Working with Deployments

## Creating Deployments

To create a deployment we can use the `kubectl create deployment` command:

```bash
# Create a NGINX Deployment named "mydeploy", with exactly 3 replicas Pods that have port 80/TCP opened.
kubectl create deployment mydeploy --image=nginx --port=80 --replicas=3
```

Don't forget that using the `--dry-run=client -o yaml` flags are your best-friends in the exam, giving you the quickest way possible the object specification:

```bash
kubectl create deployment mydeploy --image=nginx --port=80 --replicas=3 --dry-run=client -o yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
    creationTimestamp: null
    labels:
        app: mydeploy
    name: mydeploy
    spec:
    replicas: 1
    selector:
        matchLabels:
        app: mydeploy
    strategy: {}
    template:
        metadata:
        creationTimestamp: null
        labels:
            app: mydeploy
        spec:
        containers:
        - image: nginx
            name: nginx
            ports:
            - containerPort: 80
            resources: {}
    status: {}
```

## Getting Information about Deployments

You can grab information about deployments, like any other object inside kubernetes. 
Either by using the `get` option ou using the `describe`:

```bash
kubectl get deployments
    NAME       READY   UP-TO-DATE   AVAILABLE   AGE
    mydeploy   1/3     3            1           5s


kubectl get deployment mydeploy -o yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
    annotations:
        deployment.kubernetes.io/revision: "1"
    creationTimestamp: "2021-08-24T08:15:22Z"
    generation: 1
    labels:
        app: mydeploy
    name: mydeploy
    namespace: default
    resourceVersion: "137330"
    uid: XXX
    spec:
    progressDeadlineSeconds: 600
    replicas: 3
    revisionHistoryLimit: 10
    selector:
        matchLabels:
        app: mydeploy
    strategy:
        rollingUpdate:
        maxSurge: 25%
        maxUnavailable: 25%
        type: RollingUpdate
    template:
        metadata:
        creationTimestamp: null
        labels:
            app: mydeploy
        spec:
        containers:
        - image: nginx
            imagePullPolicy: Always
            name: nginx
            ports:
            - containerPort: 80
            protocol: TCP
            resources: {}
            terminationMessagePath: /dev/termination-log
            terminationMessagePolicy: File
        dnsPolicy: ClusterFirst
        restartPolicy: Always
        schedulerName: default-scheduler
        securityContext: {}
        terminationGracePeriodSeconds: 30
    status:
    availableReplicas: 3
    conditions:
    - lastTransitionTime: "2021-08-24T08:15:30Z"
        lastUpdateTime: "2021-08-24T08:15:30Z"
        message: Deployment has minimum availability.
        reason: MinimumReplicasAvailable
        status: "True"
        type: Available
    - lastTransitionTime: "2021-08-24T08:15:22Z"
        lastUpdateTime: "2021-08-24T08:15:30Z"
        message: ReplicaSet "mydeploy-666dfd4f58" has successfully progressed.
        reason: NewReplicaSetAvailable
        status: "True"
        type: Progressing
    observedGeneration: 1
    readyReplicas: 3
    replicas: 3
    updatedReplicas: 3


kubectl describe deployment mydeploy
    Name:                   mydeploy
    Namespace:              default
    CreationTimestamp:      Tue, 24 Aug 2021 09:15:22 +0100
    Labels:                 app=mydeploy
    Annotations:            deployment.kubernetes.io/revision: 1
    Selector:               app=mydeploy
    Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
    StrategyType:           RollingUpdate
    MinReadySeconds:        0
    RollingUpdateStrategy:  25% max unavailable, 25% max surge
    Pod Template:
    Labels:  app=mydeploy
    Containers:
    nginx:
        Image:        nginx
        Port:         80/TCP
        Host Port:    0/TCP
        Environment:  <none>
        Mounts:       <none>
    Volumes:        <none>
    Conditions:
    Type           Status  Reason
    ----           ------  ------
    Available      True    MinimumReplicasAvailable
    Progressing    True    NewReplicaSetAvailable
    OldReplicaSets:  <none>
    NewReplicaSet:   mydeploy-666dfd4f58 (3/3 replicas created)
    Events:
    Type    Reason             Age   From                   Message
    ----    ------             ----  ----                   -------
    Normal  ScalingReplicaSet  95s   deployment-controller  Scaled up replica set mydeploy-666dfd4f58 to 3
```

## Deleting Deployments

A common mistake k8s beginners often do, is to try and delete pods that are associated with a deployment, and end up frustrated because the Pods keep spawning. This is normal, and the intended behavior of Deployments. The only way to completely delete this object is by delete the Deployment itself:

```bash
kubectl get deployment
    NAME       READY   UP-TO-DATE   AVAILABLE   AGE
    mydeploy   3/3     3            3           4m38s


kubectl delete deployment mydeploy
    deployment.apps "mydeploy" deleted
```

## Scaling Deployments

There are 2 main ways for a user with the required permissions to scale up or down a already running deployment. Either it is done using the `kubectl scale` command, or by manually editing the Deployment object and changing the Replicas field:

```bash
# Kubectl Scale Command:
kubectl get deployment
    NAME       READY   UP-TO-DATE   AVAILABLE   AGE
    mydeploy   3/3     3            3           4m38s

kubectl scale deployment mydeploy --replicas=2

kubectl get deployment
    NAME       READY   UP-TO-DATE   AVAILABLE   AGE
    mydeploy   2/2     2            2           6m38s


# Manually editing the Deployment Object
kubectl get deployment
    NAME       READY   UP-TO-DATE   AVAILABLE   AGE
    mydeploy   2/2     2            2           6m56s

kubectl edit deployment mydeploy
    (...)
    spec:
    #  replicas: 2       <-- Old Value
      replicas: 4
    (...)

kubectl get deployment
    NAME       READY   UP-TO-DATE   AVAILABLE   AGE
    mydeploy   4/4     4            4           10m2s
```
