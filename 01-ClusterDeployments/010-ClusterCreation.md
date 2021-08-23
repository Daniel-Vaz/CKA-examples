# Kubeadm HA Cluster Creation

## Assumptions

- This guide assumes you already have 3 [Ubuntu 18.04](https://releases.ubuntu.com/18.04.5/ubuntu-18.04.5-live-server-amd64.iso) nodes ready, and you have SSH access to all of them;

- To keep it simple, it is assumed the nodes are on the same network and therefore can communicate with each other freely. 

- We are going to deploy a 3 Node cluster, with a single Master Node. Not the suggested architecture to be deployed in production, but is the topology that you will find in the CKA exam. 

- We are preparing for the CKA version 1.21 Exam.

## Steps

### Install Kubeadm

[Documentation](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)

The bellow steps need to be performed on all 3 Nodes. 

```bash
# Disable SWAP
sudo swapoff -a
# Don't forget to comment the entry in fstab.

# Allow iptables to see bridged traffic
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
br_netfilter
EOF

cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF

sudo sysctl --system

# Check if the required Ports are opened.
# K.I.S.S - Disable Firewall
sudo ufw disable

# Install "Containerd" run-time
# Docs: https://docs.docker.com/engine/install/ubuntu/
sudo apt-get update
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    lsb-release

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

echo \
  "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
sudo apt-get install containerd.io

# Configure Containerd
cat <<EOF | sudo tee /etc/modules-load.d/containerd.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter

cat <<EOF | sudo tee /etc/sysctl.d/99-kubernetes-cri.conf
net.bridge.bridge-nf-call-iptables  = 1
net.ipv4.ip_forward                 = 1
net.bridge.bridge-nf-call-ip6tables = 1
EOF

sudo sysctl --system

sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml

sudo systemctl restart containerd

## Install kubeadm, kubelet and kubectl
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl

sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg

echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt-get update
sudo apt-get install -y kubelet=1.21.4-00 kubeadm=1.21.4-00 kubectl=1.21.4-00
sudo apt-mark hold kubelet kubeadm kubectl
```

After performing all the described steps above on all the nodes we wish to convert into a cluster, we can now proceed with the deployment of the cluster itself. 
To do this follow the bellow indications:

```bash
# On the Master Node start by deploying the cluster:
sudo kubeadm init --control-plane-endpoint "MASTER_IP:MASTER_PORT" --apiserver-advertise-address "MASTER_IP" --pod-network-cidr "192.168.0.0/16" --upload-certs

# After the successful completion of the above command, a similar bellow output will be returned.
# Use this output to booth configure your kubectl tool, and to grab the command to deploy on the other 2 Nodes. 
...
Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a Pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  /docs/concepts/cluster-administration/addons/

You can now join any number of control-plane node by running the following command on each as a root:
    kubeadm join 192.168.0.200:6443 --token 9vr73a.a8uxyaju799qwdjv --discovery-token-ca-cert-hash sha256:7c2e69131a36ae2a042a339b33381c6d0d43887e2de83720eff5359e26aec866 --control-plane --certificate-key f8902e114ef118304e561c3ecd4d0b543adc226b7a07f675f56564185ffe0c07

Please note that the certificate-key gives access to cluster sensitive data, keep it secret!
As a safeguard, uploaded-certs will be deleted in two hours; If necessary, you can use kubeadm init phase upload-certs to reload certs afterward.

Then you can join any number of worker nodes by running the following on each as root:
    kubeadm join 192.168.0.200:6443 --token 9vr73a.a8uxyaju799qwdjv --discovery-token-ca-cert-hash sha256:7c2e69131a36ae2a042a339b33381c6d0d43887e2de83720eff5359e26aec866


# Since we are going to make the other 2 Nodes Workers, we grab the last provided command, and run it on the other Nodes. 
# Once completed correctly, we will see the cluster UP but the Nodes will not be in the Ready state:
kubectl get nodes
    NAME               STATUS         ROLES                  AGE   VERSION
    k8smaster.lab      NotReady       control-plane,master   47d   v1.21.2
    k8sworker01.lab    NotReady       worker                 47d   v1.21.2
    k8sworker02.lab    NotReady       worker                 47d   v1.21.2

# In the CKA detailed instructions will be provided for you to deploy the CNI.
# For the purpose of creating a test environment lets deploy the Calico CNI. 
# Docs: https://docs.projectcalico.org/getting-started/kubernetes/quickstart

# Install the Tigera Operator
kubectl create -f https://docs.projectcalico.org/manifests/tigera-operator.yaml

# Download and edit the custom-resource file, changing the CIDR of the POd Network if necessary:
wget https://docs.projectcalico.org/manifests/custom-resources.yaml
# Change the cidr field if you specified a different one in the "kubeadm init" phase!
# And apply the resulting file:
kubectl apply -f custom-resources.yaml

# Watch the deployments until all pods are ready:
watch kubectl get pods -n calico-system

# Remove the Masters Taints if desired to allow scheduling on these nodes:
kubectl taint nodes --all node-role.kubernetes.io/master-

# And make sure all nodes now report a "Ready" state
kubectl get nodes
    NAME               STATUS      ROLES                  AGE   VERSION
    k8smaster.lab      Ready       control-plane,master   47d   v1.21.2
    k8sworker01.lab    Ready       worker                 47d   v1.21.2
    k8sworker02.lab    Ready       worker                 47d   v1.21.2
```
