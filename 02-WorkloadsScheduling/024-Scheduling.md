# Scheduling Pods onto Nodes

## Working with Taints and Tolerations

Taints and tolerations work together to ensure that pods are not scheduled onto inappropriate nodes. One or more taints are applied to a node; this marks that the node should not accept any pods that do not tolerate the taints.

### Getting Nodes Taints
```bash
# Grabbing all Nodes Taints
kubectl describe nodes | grep Taint
    Taints:             <none>
```

### Adding and Removing Nodes Taints

```bash
# Adding a Taint to a Node
kubectl taint node k8stests.lab node=worker:NoSchedule
    node/k8stests.lab tainted

# Removing a Taint from a Node
kubectl taint node k8stests.lab node-
    node/k8stests.lab untainted
```

### Adding Tolerations to Pods

```bash
# Example Pod with matching Toleration for the example Taint provided above.
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx
  tolerations:
  - key: "node"
    operator: "Equal"
    value: "worker"
    effect: "NoSchedule"

# Same example but with the "Exists" Operator
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx
  tolerations:
  - key: "node"
    operator: "Exists"
    effect: "NoSchedule"
```

## Labels and NodeSelectors

You can constrain a Pod so that it can only run on particular set of Node(s). There are several ways to do this and the recommended approaches all use label selectors to facilitate the selection. nodeSelector is the simplest recommended form of node selection constraint. nodeSelector is a field of PodSpec. It specifies a map of key-value pairs. For the pod to be eligible to run on a node, the node must have each of the indicated key-value pairs as labels.

### Labeling Nodes

```bash
# Grabbing Existing Nodes Labels
## The "-A 10" shows the next 10 Lines. Change this number according the amount of Labels the Node has.
kubectl describe node | grep -A 10 Label
    Labels:             beta.kubernetes.io/arch=amd64
                        beta.kubernetes.io/os=linux
                        kubernetes.io/arch=amd64
                        kubernetes.io/hostname=k8stests.lab
                        kubernetes.io/os=linux
                        node-role.kubernetes.io/control-plane=
                        node-role.kubernetes.io/master=
                        node.kubernetes.io/exclude-from-external-load-balancers=

# Adding Label
kubectl label node k8stests.lab diskType=ssd
    node/k8stests.lab labeled

# Removing Label
kubectl label node k8stests.lab diskType-
    node/k8stests.lab labeled
```

### Scheduling Pods with nodeSelector

```bash
# Example Pod Spec with matching NodeSelector for the example Label above.
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx
  nodeSelector:
    diskType: ssd
```
