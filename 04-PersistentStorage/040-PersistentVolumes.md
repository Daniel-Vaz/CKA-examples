# Working with Persistent Volumes

Persistent Volumes aren't namespace scoped. When a pod using a PV gets deleted, that PV is still usable by other pods.

## Creating Local Volumes

In the CKA exam the most common volume types asked to be configured are "hostPath" and "local".
A hostPath volume mounts a file or directory from the host node's filesystem into your Pod.
A local volume represents a mounted local storage device such as a disk, partition or directory. Local volumes can only be used as a statically created PersistentVolume. Dynamic provisioning is not supported.

```bash
# hostPath PV Creation
cat << EOF > hostpath-pv.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: hostpath-pv
spec:
  capacity:
    storage: 100Mi
  accessModes:
  - ReadWriteOnce
  hostPath:
    path: /mnt/
EOF
kubectl create -f hostpath-pv.yaml

kubectl get pv
    NAME             CAPACITY   ACCESS MODES    RECLAIM POLICY       STATUS      CLAIM              STORAGECLASS    REASON   AGE
    hostpath-pv         100Mi      RWO            Retain           Available                                                 78s


# Local PV Creation
cat << EOF > local-pv.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: local-pv
spec:
  capacity:
    storage: 100Mi
  volumeMode: Filesystem
  accessModes:
  - ReadWriteOnce
  persistentVolumeReclaimPolicy: Delete
  local:
    path: /mnt/
  nodeAffinity:
    required:
      nodeSelectorTerms:
      - matchExpressions:
        - key: beta.kubernetes.io/os
          operator: In
          values:
          - linux
EOF
kubectl create -f local-pv.yaml

kubectl get pv
    NAME       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
    local-pv   100Mi      RWO            Delete           Available                                   4s
```

## Deleting PersistentVolumes

```bash
kubectl get pv
    NAME       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
    local-pv   100Mi      RWO            Delete           Available                                   4s

kubectl delete pv local-pv
    persistentvolume "local-pv" deleted
```
