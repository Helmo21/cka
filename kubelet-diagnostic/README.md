# kubelet: a component that runs on all machines in your cluster and performs operations such as starting pods and containers

# cluster side 

kubectl get nodes

kubectl describe node <node>

# Connect to node with SSH

ssh <node>

# Systemd (always kubelet)

systemctl status kubelet

systemctl restart kubelet

systemctl enable kubelet

journalctl -xeu kubelet (ou équivalent pour logs)

# Filesystem

Pour corriger :

inspecter /var/lib/kubelet

inspecter /etc/kubernetes/kubelet.conf

inspecter les certificats / config

# Validation

kubectl get nodes (jusqu’à Ready)

kubectl describe node (pas de warnings restants)