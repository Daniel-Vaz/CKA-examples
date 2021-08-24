# Working with Secrets

## Creating Secrets

There are two most common ways to create secrets, either by specifying literal key/value pairs, or by providing entire Files to be used.
Booth ways use the `kubectl create secret` command, the difference is in the flags, `--from-literal=[KEY]=[VALUE]` and `--from-file=[KEY]=[FILE_NAME]`.

```bash
kubectl create secret generic literal-secret --from-literal=MyKey=MyValue
    secret/literal-secret created

kubectl create secret generic file-secret --from-file=nginxconf=/etc/nginx.conf
    secret/file-secret created
```

## Getting Secrets Information

Like all other objects information about secrets can be gathered using the `get` and `describe` options.

```bash
kubectl get secret literal-secret -o yaml
    apiVersion: v1
    data:
    MyKey: TXlWYWx1ZQ==
    kind: Secret
    metadata:
    creationTimestamp: "2021-08-24T08:44:38Z"
    name: literal-secret
    namespace: default
    resourceVersion: "141228"
    uid: XXX
    type: Opaque


kubectl describe secret literal-secret
    Name:         literal-secret
    Namespace:    default
    Labels:       <none>
    Annotations:  <none>

    Type:  Opaque

    Data
    ====
    MyKey:  7 bytes
```

## Deleting Secrets

Delete secrets using the `delete` option.

```bash
kubectl get secret
    NAME                       TYPE                                  DATA   AGE
    literal-secret             Opaque                                1      67s

kubectl delete secret literal-secret
    secret "literal-secret" deleted
```

## Using Secrets in Pods

There are two main ways of using Secrets inside Pods, mainly, either by mounting the secret as a volume in the Containers, ou by using the information inside the secret as environment variables inside containers:

```bash
# Mounting as Volume
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: redis
    volumeMounts:
    - name: volumename
      mountPath: "/etc/secrets"
      readOnly: true
  volumes:
  - name: volumename
    secret:
      secretName: literal-secret

# Using as Environment Variable
apiVersion: v1
kind: Pod
metadata:
  name: secret-env-pod
spec:
  containers:
  - name: mycontainer
    image: redis
    env:
      - name: MYKEY
        valueFrom:
          secretKeyRef:
            name: literal-secret
            key: MyKey
  restartPolicy: Never
```
