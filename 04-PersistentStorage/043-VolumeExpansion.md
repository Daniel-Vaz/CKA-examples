# Expanding existing Volumes

For a Volume to be expanded it needs to firstly be deployed and be associated with a StorageClass that allows for such expansion.
The common steps, are the ones described bellow:

1. Firstly Create a StorageClass:

```bash
cat << EOF > sc-expand.yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: local-storage-expand
provisioner: kubernetes.io/no-provisioner
allowVolumeExpansion: true
EOF
kubectl create -f sc-expand.yaml
```

2. Then create a PVC:

```bash
cat << EOF > pvc-resize.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-tobe-resized
spec:
  accessModes:
    - ReadWriteOnce
  volumeMode: Filesystem
  resources:
    requests:
      storage: 100Mi
  storageClassName: local-storage-expand
EOF
kubectl create -f pvc-resize.yaml
```

3. Create a Pod that consumes the PVC

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
        claimName: pvc-tobe-resized
EOF
kubectl create -f pod-pvc.yaml
```

4. Edit the PVC and change the Requested amount of storage:

```bash
kubectl edit pvc pvc-tobe-resized
# Change the value under spec.resources.requests.storage
# To a higher value
```

5. Restart the Pod using the PVc and see that a the PV bound to the PVC after a While will get the new value. 


NOTE: This guide assumes that you are using a Storage Class that can be dynamically provisioned. If using the SC provided in the example bellow the PV creation will need to be manual, and consequently the recreation of the PV after the edit of the PVC should be mandatory also. 
