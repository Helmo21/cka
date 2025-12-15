## Required information before etcd backup

Before taking an etcd snapshot, always identify:
1. The etcd endpoint(s)
2. The etcd data directory (data-dir)
3. The TLS certificates required to access etcd (CA, cert, key)

/etc/kubernetes/manifests/etcd.yaml

## Why restore etcd to a new data directory

Always restore an etcd snapshot into a new data-dir:
- to avoid corrupting the existing state
- to ensure the restore is atomic
- to allow rollback if needed before restarting etcd


etcdctl snapshot save
etcdctl snapshot status
etcdctl snapshot restore

## Commands (cheat)
### Set API version for etcdctl

export ETCDCTL_API=3

# Taking snapshot 

sudo etcdctl \
  --endpoints=<ENDPOINTS> \
  --cacert=<CA_CERT> \
  --cert=<CLIENT_CERT> \
  --key=<CLIENT_KEY> \
  snapshot save /opt/etcd-backup.db

# Verify the snapshot
sudo etcdctl snapshot status /opt/etcd-backup.db -w table

# Restore snapshot into a NEW data-dir
Pick a clean restore directory

sudo etcdctl snapshot restore /opt/etcd-backup.db \
  --data-dir=/var/lib/etcd-from-backup

# Point etcd static pod to the restored data-dir
Edit the static pod manifest

sudo vi /etc/kubernetes/manifests/etcd.yaml

Update the --data-dir=... argument to the restored directory

# Validate the cluster recover 

kubectl get nodes
kubectl get pods -A
kubectl -n kube-system get pods
