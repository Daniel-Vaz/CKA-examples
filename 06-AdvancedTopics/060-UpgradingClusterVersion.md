# Upgrading Cluster Version

Remember that in the CKA exam most of the cluster are composed by just 1 Master and 2 Worker Nodes. 
There aren't cluster with more than 1 Master Node. 
Seeing this, the bellow example will go over a cluster with the exact topology specified above. 

## Upgrading Control Plane Nodes

```bash
# Firstly grab the requested version that is asked for the cluster to be updated to.
# Then proceed to upgrade the kubeadm package
apt-get update && \
apt-get install -y --allow-change-held-packages kubeadm=1.21.x-00

# Verify and prepare the upgrade plan
kubeadm version
kubeadm upgrade plan

# Run the initial Upgrade
sudo kubeadm upgrade apply v1.21.x

# Drain the Node
kubectl drain <node-to-drain> --ignore-daemonsets

# Upgrade Kubelet and Kubectl
apt-get update && \
apt-get install -y --allow-change-held-packages kubelet=1.21.x-00 kubectl=1.21.x-00

# Restart Kubelet
sudo systemctl daemon-reload
sudo systemctl restart kubelet

# Uncordon the Node
kubectl uncordon <node-to-drain>
```

## Upgrading Worker Nodes

> Note: These steps must be performed after all Control Plane nodes have been successfully upgraded first. 

```bash
# Upgrade Kubeadm
apt-get update && \
apt-get install -y --allow-change-held-packages kubeadm=1.21.x-00

# Verify and prepare the Upgrade Plan
kubeadm version
sudo kubeadm upgrade node

# Drain the Node
kubectl drain <node-to-drain> --ignore-daemonsets

# Upgrade Kubelet and Kubectl
apt-get update && \
apt-get install -y --allow-change-held-packages kubelet=1.21.x-00 kubectl=1.21.x-00

# Restart Kubelet
sudo systemctl daemon-reload
sudo systemctl restart kubelet

# Uncordon the Node
kubectl uncordon <node-to-drain>
```
