# Working with Persistent Volume Claims

A PersistentVolumeClaim (PVC) is a request for storage by a user. It is similar to a Pod. Pods consume node resources and PVCs consume PV resources. Pods can request specific levels of resources (CPU and Memory). Claims can request specific size and access modes.

## Creating PersistentVolumeClaims

With PVC's you can either provision first a StorageClass and then request in the PVC a PV to that same StorageClass, or you can firstly create a PV and then create a PVC that complies with the already created PV. If everything is done correctly the booth resources will be Bound, adn the PVC can be later on mounted in a Pod. 

```bash
# Firstly Create a PV
cat << EOF > pv.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-for-pvc
spec:
  capacity:
    storage: 100Mi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/"
EOF
kubectl create -f pv.yaml

kubectl get pv
    NAME         CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
    pv-for-pvc   100Mi      RWO            Retain           Available                                   3s

# Then create a PVC that requests a amount of storage that is Lower than the one our PV provides
cat << EOF > pvc.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-example
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 50Mi
EOF
kubectl create -f pvc.yaml

kubectl get pv,pvc
    NAME                          CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                 STORAGECLASS   REASON   AGE
    persistentvolume/pv-for-pvc   100Mi      RWO            Retain           Bound    default/pvc-example                           90s

    NAME                                STATUS   VOLUME       CAPACITY   ACCESS MODES   STORAGECLASS   AGE
    persistentvolumeclaim/pvc-example   Bound    pv-for-pvc   100Mi      RWO                           4s
```

## Deleting PersistenVolumeClaims

```bash
kubectl get pvc
    NAME          STATUS   VOLUME       CAPACITY   ACCESS MODES   STORAGECLASS   AGE
    pvc-example   Bound    pv-for-pvc   100Mi      RWO                           95s

kubectl delete pvc pvc-example
    persistentvolumeclaim "pvc-example" deleted
```
