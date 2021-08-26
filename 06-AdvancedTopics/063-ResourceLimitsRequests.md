# Working with Resources Limits and Requests

If the node where a Pod is running has enough of a resource available, it's possible (and allowed) for a container to use more resource than its request for that resource specifies. However, a container is not allowed to use more than its resource limit.

Each Container of a Pod can specify one or more of the following:

- `spec.containers[].resources.limits.cpu`

- `spec.containers[].resources.limits.memory`

- `spec.containers[].resources.limits.hugepages-<size>`

- `spec.containers[].resources.requests.cpu`

- `spec.containers[].resources.requests.memory`

- `spec.containers[].resources.requests.hugepages-<size>`

Although requests and limits can only be specified on individual Containers, it is convenient to talk about Pod resource requests and limits. A Pod resource request/limit for a particular resource type is the sum of the resource requests/limits of that type for each Container in the Pod.

```bash
# Example Pod Spec with Requests and Limits Specified
apiVersion: v1
kind: Pod
metadata:
  name: myapp
spec:
  containers:
  - name: myapp-container1
    image: nginx:latest
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
  - name: myapp-container2
    image: nginx:latest
    resources:
      requests:
        memory: "150Mi"
        cpu: "500m"
      limits:
        memory: "200Mi"
        cpu: "600m"
```
