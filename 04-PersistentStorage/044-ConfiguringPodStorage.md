# Configuring Persistent Storage in Pods

## Pods consuming hostPath Volumes

A hostPath volume mounts a file or directory from the host node's filesystem into your Pod. 

```bash
cat << EOF > hostpath.yaml
apiVersion: v1
kind: Pod
metadata:
  name: hostpath-pd
spec:
  containers:
  - image: nginx
    name: nginx-web
    volumeMounts:
    - mountPath: /test-pd
      name: hostpath-volume
  volumes:
  - name: hostpath-volume
    hostPath:
      path: /data
      type: Directory
EOF
kubectl create -f hostpath.yaml
```

## Pods consuming emptyDir Volumes

An emptyDir volume is first created when a Pod is assigned to a node, and exists as long as that Pod is running on that node. As the name says, the emptyDir volume is initially empty.

```bash
cat << EOF > emptydir.yaml
apiVersion: v1
kind: Pod
metadata:
  name: emptydir-pd
spec:
  containers:
  - image: nginx
    name: nginx-web
    volumeMounts:
    - mountPath: /cache
      name: cache-volume
  volumes:
  - name: cache-volume
    emptyDir: {}
EOF
kubectl create -f emptydir.yaml
```

## Pods consuming Persistent Volume Claims

Firstly create a PVC named "myclaim" and make sure it is bound to a existing (or dynamically created) PV. 

```bash
cat << EOF > pod-pvc.yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/var/www/html"
        name: mypd
  volumes:
    - name: mypd
      persistentVolumeClaim:
        claimName: myclaim
EOF
kubectl create -f pod-pvc.yaml
```
