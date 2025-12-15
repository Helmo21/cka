## Special case: kubelet running but node NotReady

If kubelet is running but the node is NotReady:
- Always inspect the node Conditions.
- If Reason is ContainerRuntimeNotReady:
  - The problem is NOT kubelet itself.
  - Check the container runtime dependency.

Key rule:
kubelet communicates with the container runtime via a local CRI Unix socket, not the network.

Common causes:
- runtime service down
- wrong socket path configured
- socket exists but permission denied

Fix:
- restore correct socket permissions so kubelet can access the runtime.




# Identify failing control-plane components

kubectl get pods -n kube-system
kubectl get componentstatuses

# Inspect control-plane pod logs

kubectl logs -n kube-system <pod-name>

# Locate static pod manifests (on control-plane node)

ls /etc/kubernetes/manifests/

# Inspect a static pod manifest

sudo cat /etc/kubernetes/manifests/<component>.yaml

# Observe kubelet reaction to changes

journalctl -u kubelet

# Validate recovery

kubectl get nodes
kubectl get pods -n kube-system


###################################### 

### Case 1: Static pod crash-loop

Symptoms:

 Pod in CrashLoopBackOff

 Control-plane partially unavailable

Cause examples:

 Invalid flag in manifest

 Bad certificate path

 Missing file referenced by the manifest

Fix:

 Correct the manifest

 Kubelet recreates the pod automatically

###################################### 

### Case 2: Control-plane pod missing

Symptoms:

 Pod not listed in kubectl get pods -n kube-system

 API Server unreachable (if kube-apiserver missing)

Likely cause:

 Manifest deleted or renamed

 Kubelet cannot read the manifest

Fix:

 Restore the manifest in /etc/kubernetes/manifests/

 Ensure correct filename and permissions

###################################### 

### Case 3: Wrong certificate or config file

Symptoms:

 TLS errors in logs

 API Server or controller-manager not starting

Cause:

 xpired or missing cert

 Wrong path in manifest flags

Fix:

 Restore correct certificate paths

 Ensure referenced files exist and are readable

###################################### 

### Case 4: Static pod edited incorrectly

Symptoms:

 Component stops working immediately after edit

Cause:

 YAML syntax error

 Invalid flag

Fix:

 Revert the change

 Kubelet will recreate the pod

###################################### 

### Case 5: kubelet restart and static pods

Important behavior:

 Restarting kubelet will restart ALL static pods

 Control-plane downtime is expected briefly

 This is normal and not an error.