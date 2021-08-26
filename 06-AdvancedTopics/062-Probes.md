# Working with Probes

## Liveness Probes

The kubelet uses liveness probes to know when to restart a container. For example, liveness probes could catch a deadlock, where an application is running, but unable to make progress. Restarting a container in such a state can help to make the application more available despite bugs.

```bash
# Example Pod Specification using the Command Probe
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: nginx
  name: liveness-command
spec:
  containers:
  - name: liveness-nginx
    image: nginx:latest
    args: ['/bin/sh', '-c', 'touch /tmp/healthy; sleep 1d']
    livenessProbe:
      exec:
        command:
        - cat
        - /tmp/healthy
      initialDelaySeconds: 5
      periodSeconds: 5

# Example Pod Specification using the HTTP Probe
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: nginx
  name: liveness-http
spec:
  containers:
  - name: liveness-nginx
    image: nginx:latest
    ports:
    - containerPort: 80
    livenessProbe:
      httpGet:
        path: /
        port: 80
      initialDelaySeconds: 5
      periodSeconds: 5

# Example Pod Specification using the TCP Probe
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: nginx
  name: liveness-tcp
spec:
  containers:
  - name: liveness-nginx
    image: nginx:latest
    ports:
    - containerPort: 80
    livenessProbe:
      tcpSocket:
        port: 80
      initialDelaySeconds: 5
      periodSeconds: 5
```

## Readiness Probes

The kubelet uses readiness probes to know when a container is ready to start accepting traffic. A Pod is considered ready when all of its containers are ready. One use of this signal is to control which Pods are used as backends for Services. When a Pod is not ready, it is removed from Service load balancers.

```bash
# Example Pod Specification using the Command Probe
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: nginx
  name: readiness-command
spec:
  containers:
  - name: readiness-nginx
    image: nginx:latest
    args: ['/bin/sh', '-c', 'touch /tmp/healthy; sleep 1d']
    readinessProbe:
      exec:
        command:
        - cat
        - /tmp/healthy
      initialDelaySeconds: 5
      periodSeconds: 5

# Example Pod Specification using the HTTP Probe
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: nginx
  name: readiness-http
spec:
  containers:
  - name: readiness-nginx
    image: nginx:latest
    ports:
    - containerPort: 80
    readinessProbe:
      httpGet:
        path: /
        port: 80
      initialDelaySeconds: 5
      periodSeconds: 5

# Example Pod Specification using the TCP Probe
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: nginx
  name: readiness-tcp
spec:
  containers:
  - name: readiness-nginx
    image: nginx:latest
    ports:
    - containerPort: 80
    readinessProbe:
      tcpSocket:
        port: 80
      initialDelaySeconds: 5
      periodSeconds: 5
```
