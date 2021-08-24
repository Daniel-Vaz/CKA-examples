# Working with ConfigMaps

## Creating ConfigMaps

There are two most common ways to create configmaps, either by specifying literal key/value pairs, or by providing entire Files to be used.
Booth ways use the `kubectl create configmap` command, the difference is in the flags, `--from-literal=[KEY]=[VALUE]` and `--from-file=[KEY]=[FILE_NAME]`.

```bash
kubectl create configmap literal-config --from-literal=MyKey=MyValue
    configmap/literal-config created

kubectl create configmap file-config --from-file=nginxconf=/etc/nginx.conf
    configmap/file-config created
```

## Getting ConfigMaps Information

Like all other objects information about configmaps can be gathered using the `get` and `describe` options.

```bash
kubectl get configmap literal-config -o yaml
    apiVersion: v1
    data:
        MyKey: MyValue
        kind: ConfigMap
    metadata:
        creationTimestamp: "2021-08-24T08:33:01Z"
        name: literal-config
        namespace: default
        resourceVersion: "139714"
        uid: XXX


kubectl describe configmap literal-config
    Name:         literal-config
    Namespace:    default
    Labels:       <none>
    Annotations:  <none>

    Data
    ====
    MyKey:
    ----
    MyValue
    Events:  <none>
```

## Deleting ConfigMaps

Delete configmaps using the `delete` option.

```bash
kubectl get configmap
    NAME               DATA   AGE
    literal-config     1      4m51s

kubectl delete configmap literal-config
    configmap "literal-config" deleted
```

## Using ConfigMaps on Pods

There are two main ways of using Configmaps inside Pods, mainly, either by mounting the configmap as a volume in the Containers, ou by using the information inside the configmaps as environment variables inside containers:

```bash
# Mounting as Volumes:
apiVersion: v1
kind: Pod
metadata:
  name: config-pod-volume
spec:
  containers:
    - name: nginx
      image: nginx:latest
      volumeMounts:
      - name: config-volume
        mountPath: /etc/config
  volumes:
    - name: config-volume
      configMap:
        name: configmap-name
  restartPolicy: Never

# Using as Environment Variables:
apiVersion: v1
kind: Pod
metadata:
  name: config-pod-env
spec:
  containers:
    - name: nginx
      image: nginx:latest
      env:
        - name: MYKEY
          valueFrom:
            configMapKeyRef:
              name: literal-config
              key: MyKey
  restartPolicy: Never
```
