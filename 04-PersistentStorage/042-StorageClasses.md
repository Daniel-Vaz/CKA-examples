# Working with StorageClasses

A StorageClass provides a way for administrators to describe the "classes" of storage they offer. Different classes might map to quality-of-service levels, or to backup policies, or to arbitrary policies determined by the cluster administrators.

## Creating StorageClasses

The most common StorageClass type that you are going to use in the CKA exam is the "Local" type. 
This type of StorageClass is a bit of a dummy option, for later use when configuring PV's and PVC's that use the Local Volumes types. Pay attention to the fact that the provisioners on the bellow example is in fact "no-provisioner". 

```bash
# Normal Local Storage Class
cat << EOF > sc.yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: local-storage
provisioner: kubernetes.io/no-provisioner
volumeBindingMode: WaitForFirstConsumer
EOF
kubectl create -f sc.yaml

kubectl get sc
    NAME            PROVISIONER                    RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
    local-storage   kubernetes.io/no-provisioner   Delete          WaitForFirstConsumer   false                  4s

# Local Storage Class that allows for future Volume Expansion 
cat << EOF > sc-expand.yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: local-storage-expand
provisioner: kubernetes.io/no-provisioner
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF
kubectl create -f sc-expand.yaml
```

## Deleting StorageClasses

```bash
kubectl get sc
    NAME            PROVISIONER                    RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
    local-storage   kubernetes.io/no-provisioner   Delete          WaitForFirstConsumer   false                  4s

kubectl delete sc local-storage
    storageclass.storage.k8s.io "local-storage" deleted
```
