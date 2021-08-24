# Working with Ingress Controllers and Ingress Resources

## Creating Ingress Objects

Ingress objects can also be created using the `kubectl create ingress` command, but normally a bse config is copied from the kubernetes documentation and from there is edited to do exactly was it is asked\desired. 
Bellow are some example of Ingress Objects:

```bash
# Ingress all traffic coming to the cluster and requesting the "/testpath" to the "test" service on port "80":
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: minimal-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - http:
      paths:
      - path: /testpath
        pathType: Prefix
        backend:
          service:
            name: test
            port:
              number: 80

# Ingress with multiple Host configurations:
#   - Requests to the host "foo.bar.com" will go to service "service1" on port "80".
#   - Requests to the wildcard host "*.foo.com" on path "/foo" will go to the service "service2" on port "80".
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-wildcard-host
spec:
  rules:
  - host: "foo.bar.com"
    http:
      paths:
      - pathType: Prefix
        path: "/bar"
        backend:
          service:
            name: service1
            port:
              number: 80
  - host: "*.foo.com"
    http:
      paths:
      - pathType: Prefix
        path: "/foo"
        backend:
          service:
            name: service2
            port:
              number: 80
```

Don't forget to create the above objects using the `kubectl create -f [FILE_NAME]`.

## Getting Ingress Information

```bash
kubectl get ingress
    NAME              CLASS    HOSTS   ADDRESS   PORTS   AGE
    minimal-ingress   <none>   *                 80      5s

kubectl get ingress minimal-ingress -o yaml
    apiVersion: networking.k8s.io/v1
    kind: Ingress
    metadata:
    annotations:
        nginx.ingress.kubernetes.io/rewrite-target: /
    creationTimestamp: "2021-08-24T09:10:57Z"
    generation: 1
    name: minimal-ingress
    namespace: default
    resourceVersion: "144687"
    uid: XXX
    spec:
    rules:
    - http:
        paths:
        - backend:
            service:
                name: test
                port:
                number: 80
            path: /testpath
            pathType: Prefix
    status:
    loadBalancer: {}


kubectl describe ingress minimal-ingress
    Name:             minimal-ingress
    Namespace:        default
    Address:
    Default backend:  default-http-backend:80 (<error: endpoints "default-http-backend" not found>)
    Rules:
    Host        Path  Backends
    ----        ----  --------
    *
                /testpath   test:80 
    Annotations:  nginx.ingress.kubernetes.io/rewrite-target: /
```

## Deleting Ingress

```bash
kubectl get ingress
    NAME              CLASS    HOSTS   ADDRESS   PORTS   AGE
    minimal-ingress   <none>   *                 80      5s

kubectl delete ingress minimal-ingress
    ingress.networking.k8s.io "minimal-ingress" deleted
```
