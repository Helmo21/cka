### Case: Certificate expired on a static pod (kubeadm)

Symptoms:
- Control-plane component starts but logs show "certificate expired"
- API Server may be partially or fully unavailable

Cause:
- TLS certificate managed by kubeadm has expired

Fix:
- Renew the certificate using kubeadm
- kubelet automatically restarts the static pod after cert renewal




# Static Pods – Control Plane (CKA)

## What CKA tests
The CKA evaluates whether you:
- Understand what static pods are and how they differ from regular pods.
- Know how kubeadm deploys control-plane components.
- Can debug a broken control-plane without relying on kubectl.
- Know that kubelet, not the API Server, manages static pods.
- Can safely fix configuration, certificate, or path issues.

This is a **cluster architecture and troubleshooting** topic, not an application workload topic.

---

## Mental model (must be automatic)
- **Static pods are managed directly by kubelet**
- **Their source of truth is a local manifest file**
- **Editing the manifest triggers an automatic restart**

Golden rule:
> If a control-plane component is failing, inspect its static pod manifest.

Always follow:
1) Identify the failing control-plane component  
2) Locate the static pod manifest  
3) Inspect logs and manifest configuration  
4) Fix ONE issue at a time  
5) Let kubelet recreate the pod  
6) Validate cluster recovery  

---

## What is a static pod
A static pod is:
- Defined by a YAML file on the node filesystem
- Created and restarted by kubelet
- Not stored in etcd
- Not editable via kubectl

Static pods appear in `kubectl get pods` as *mirror pods*, but:
- `kubectl edit pod` has no effect
- `kubectl delete pod` only causes kubelet to recreate the same broken pod

---

## Where static pods live (kubeadm clusters)
On kubeadm-based clusters, control-plane components are static pods defined in:

```text
/etc/kubernetes/manifests/







# Commands (cheat – essential only)
Identify failing control-plane components
kubectl get pods -n kube-system
kubectl get componentstatuses

# Inspect logs (if API Server is reachable)
kubectl logs -n kube-system <pod-name>

# Locate static pod manifests (on control-plane node)
ls /etc/kubernetes/manifests/

# Inspect a static pod manifest
sudo cat /etc/kubernetes/manifests/<component>.yaml

# Observe kubelet behavior
journalctl -u kubelet

# Validate recovery
kubectl get nodes
kubectl get pods -n kube-system
