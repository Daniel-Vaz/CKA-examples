# Working with NetworkPolicies

NetworkPolicies are an application-centric construct which allow you to specify how a pod is allowed to communicate with various network "entities" over the network.
The entities that a Pod can communicate with are identified through a combination of the following 3 identifiers:

1. Other pods that are allowed (exception: a pod cannot block access to itself);

2. Namespaces that are allowed;

3. IP blocks.

## Default Policies

### Default Deny All Ingress

```bash
---
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-ingress
spec:
  podSelector: {}
  policyTypes:
  - Ingress
```

### Default Deny All Egress

```bash
---
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-egress
spec:
  podSelector: {}
  policyTypes:
  - Egress
```

### Default Deny All Ingress and Egress Traffic

```bash
---
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-all
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  - Egress
```

### Default Allow All Ingress

```bash
---
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-all-ingress
spec:
  podSelector: {}
  ingress:
  - {}
  policyTypes:
  - Ingress
```

### Default Allow All Egress

```bash
---
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-all-egress
spec:
  podSelector: {}
  egress:
  - {}
  policyTypes:
  - Egress
```

## Controlling Specific Ports\Services

The bellow Example network Policy taken from the official Kubernetes documentation, demonstrates a fairly complex policy that shows all the possibilities regarding the specification of the Traffic (IPBlock\Namespace\Labels), and the usage of rules applied to booth Ingress and Egress at the same time. 

```bash
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: test-network-policy
  namespace: default
spec:
  podSelector:
    matchLabels:
      role: db
  policyTypes:
  - Ingress
  - Egress
  ingress:
  - from:
    - ipBlock:
        cidr: 172.17.0.0/16
        except:
        - 172.17.1.0/24
    - namespaceSelector:
        matchLabels:
          project: myproject
    - podSelector:
        matchLabels:
          role: frontend
    ports:
    - protocol: TCP
      port: 6379
  egress:
  - to:
    - ipBlock:
        cidr: 10.0.0.0/24
    ports:
    - protocol: TCP
      port: 5978
```

> Important Note: Please pay attention to the difference bellow:

```bash
# When you specify multiple conditions this way:
(...)
spec:
  ingress:
  - from:
    - ipBlock:
        cidr: 172.17.0.0/16
    - namespaceSelector:
        matchLabels:
          project: myproject

## With the above example, the IPBlock and Namespace Selectors are compounded using a **OR** Operator. 
## If you desire the condition to use the **AND** Operator, you need to specify the multiple conditions as the bellow example:
(...)
spec:
  ingress:
  - from:
    - ipBlock:
        cidr: 172.17.0.0/16
      namespaceSelector:
        matchLabels:
          project: myproject
```
