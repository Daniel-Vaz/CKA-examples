# Backup and Restore of the ETCD in a Cluster

```bash
# Install the etcd-client package
apt-get install etcd-client

# Backup the ETCd inside the cluster:
## Remember to grab the required flags information from the Static Manifest File. 
ETCDCTL_API=3 etcdctl snapshot save /tmp/my-etcd-backup.db \
    --cacert /etc/kubernetes/pki/etcd/ca.crt \
    --cert /etc/kubernetes/pki/etcd/server.crt \
    --key /etc/kubernetes/pki/etcd/server.key 

# Restore from a previously taken backup
ETCDCTL_API=3 etcdctl snapshot restore /tmp/my-etcd-backup.db \
                      --cacert /etc/kubernetes/pki/etcd/ca.crt \
                      --cert /etc/kubernetes/pki/etcd/server.crt \
                      --key /etc/kubernetes/pki/etcd/server.key \
                      --name [NAME] \
                      --initial-cluster-token etcd-cluster-1 \
                      --data-dir [/var/lib/newetcd] \
                      --initial-cluster [MASTER]=[IP:PORT] \
                      --initial-advertise-peer-urls https://[IP:PORT]

# All of the required flags of the restore command can also be gathered from the ETCd static Manifest.
# Edit the ETCd Manifest to change the new data-dir location and other required changes:
vi /etc/kubernetes/manifests/etcd.yaml
## Change de --data-dir= Flag to the new value
## Don't forget to change the VolumeMounts and the HostPath respectively
    --data-dir=/var/lib/newetcd
    (...)
    - --initial-cluster-token=etcd-cluster-1
    (...)
    volumeMounts:
    - mountPath: /var/lib/newetcd
    (...)
    volumes:
    - hostPath:
        path: /var/lib/newetcd

# Restart Kubelet to force the deployment of the new manifest
systemctl restart kubelet
```

After the described steps above, be patience and see if the etcd container starts on the Master Nodes. 
If everything was done correctly in about 5-10 minutes the cluster will be operational once again based on the Restore Image used. 
If the cluster doesn't respond in a reasonable time-frame, look into the etcd container logs using either "docker" or "crictl" CLI's to troubleshoot what went wrong. 