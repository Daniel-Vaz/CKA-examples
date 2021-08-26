# Working with Role Based Access Control 

An RBAC Role or ClusterRole contains rules that represent a set of permissions. Permissions are purely additive (there are no "deny" rules).
A Role always sets permissions within a particular namespace; when you create a Role, you have to specify the namespace it belongs in.
ClusterRole, by contrast, is a non-namespaced resource. The resources have different names (Role and ClusterRole) because a Kubernetes object always has to be either namespaced or not namespaced; it can't be both.


## Creating Roles and ClusterRoles

```bash
# Creating a Role using Ad-Hoc Command
## DryRun is used to show the Role Specification
kubectl create role pod-reader --verb=get,list,watch --resource=pods --dry-run=client -o yaml
    apiVersion: rbac.authorization.k8s.io/v1
    kind: Role
    metadata:
    creationTimestamp: null
    name: pod-reader
    rules:
    - apiGroups:
      - ""
      resources:
      - pods
      verbs:
      - get
      - list
      - watch
    
# Creating a ClusterRole using Ad-Hoc Command
## DryRun is used to show the ClusterRole Specification
kubectl create clusterrole pod-reader --verb=get,list,watch --resource=pods --dry-run=client -o yaml
    apiVersion: rbac.authorization.k8s.io/v1
    kind: ClusterRole
    metadata:
      creationTimestamp: null
      name: pod-reader
    rules:
    - apiGroups:
      - ""
      resources:
      - pods
      verbs:
      - get
      - list
      - watch
```

## Applying Roles and ClusterRoles

A role binding grants the permissions defined in a role to a user or set of users. It holds a list of subjects (users, groups, or service accounts), and a reference to the role being granted. A RoleBinding grants permissions within a specific namespace whereas a ClusterRoleBinding grants that access cluster-wide.

```bash
# RoleBinding of the "pod-reader" Role to the ServiceAccount "reader" in the "default" Namespace
kubectl create rolebinding pod-reader-binding --role=pod-reader --serviceaccount=default:reader -n default --dry-run=client -o yaml
    apiVersion: rbac.authorization.k8s.io/v1
    kind: RoleBinding
    metadata:
      creationTimestamp: null
      name: pod-reader-binding
      namespace: default
    roleRef:
      apiGroup: rbac.authorization.k8s.io
      kind: Role
      name: pod-reader
    subjects:
    - kind: ServiceAccount
      name: reader
      namespace: default

# ClusterRoleBinding of the "pod-reader" ClusterRole to the User "reader"
kubectl create rolebinding pod-reader-clusterbinding --clusterrole=pod-reader --user=reader --dry-run=client -o yaml
    apiVersion: rbac.authorization.k8s.io/v1
    kind: RoleBinding
    metadata:
      creationTimestamp: null
      name: pod-reader-clusterbinding
    roleRef:
      apiGroup: rbac.authorization.k8s.io
      kind: ClusterRole
      name: pod-reader
    subjects:
    - apiGroup: rbac.authorization.k8s.io
      kind: User
      name: reader
```
