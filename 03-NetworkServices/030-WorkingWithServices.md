# Working with Services

## Creating Services

A user with the required permissions can create Services in two ways, either by exposing a existing Deployment, or by creating a service manually that specifies the labels that directly connect the service to the respective Pods hosting the application. 

```bash
# Exposing a existing deployment
kubectl get deployment
    NAME       READY   UP-TO-DATE   AVAILABLE   AGE
    mydeploy   2/2     2            2           29m

kubectl expose deployment mydeploy --port=80 --type=ClusterIP

kubectl get services 
    NAME         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
    mydeploy     ClusterIP   10.109.43.43   <none>        80/TCP    2s


# Creating a Service Manually
kubectl get deployment mydeploy -o yaml
    (...)
    spec:
      template:
        metadata:
          labels:
            app: mydeploy
    (...)

kubectl create service clusterip myservice --tcp=80 --dry-run=client -o yaml
    apiVersion: v1
    kind: Service
    metadata:
    creationTimestamp: null
    labels:
        app: myservice
    name: myservice
    spec:
    ports:
    - name: "80"
        port: 80
        protocol: TCP
        targetPort: 80
    selector:
    # Change the bellow Selector, based on the one gathered above from the deployment template.
    #    app: myservice       <--- Old Value
      app: mydeploy  
    type: ClusterIP
    status:
    loadBalancer: {}
```

## Getting Services Information

Either use the `get` or the `describe` options:

```bash
kubectl get service
    NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
    mydeploy     ClusterIP   10.99.225.108   <none>        80/TCP    5s

kubectl get service mydeploy -o yaml
    apiVersion: v1
    kind: Service
    metadata:
    creationTimestamp: "2021-08-24T08:59:52Z"
    labels:
        app: mydeploy
    name: mydeploy
    namespace: default
    resourceVersion: "143228"
    uid: XXX
    spec:
    clusterIP: 10.99.225.108
    clusterIPs:
    - 10.99.225.108
    ipFamilies:
    - IPv4
    ipFamilyPolicy: SingleStack
    ports:
    - port: 80
        protocol: TCP
        targetPort: 80
    selector:
        app: mydeploy
    sessionAffinity: None
    type: ClusterIP
    status:
    loadBalancer: {}

kubectl describe service mydeploy
    Name:              mydeploy
    Namespace:         default
    Labels:            app=mydeploy
    Annotations:       <none>
    Selector:          app=mydeploy
    Type:              ClusterIP
    IP Family Policy:  SingleStack
    IP Families:       IPv4
    IP:                10.99.225.108
    IPs:               10.99.225.108
    Port:              <unset>  80/TCP
    TargetPort:        80/TCP
    Endpoints:         10.10.142.62:80,10.10.142.63:80
    Session Affinity:  None
    Events:            <none>
```

## Deleting Services

```bash
kubectl get service
    NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
    mydeploy     ClusterIP   10.99.225.108   <none>        80/TCP    5s

kubectl delete service mydeploy
    service "mydeploy" deleted
```
