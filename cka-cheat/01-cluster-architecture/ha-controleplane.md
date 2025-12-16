# High Availability Control Plane (CKA)

## What CKA tests
The CKA tests whether you understand:
- How a control plane can be made highly available (HA).
- The role of multiple API servers and etcd members.
- The use of load balancers for a unified control-plane endpoint.
- The concept of quorum for etcd in HA setups.

This is *architectural knowledge*, not a deep step-by-step install.

---

## Mental model (automatic)
High availability means:
1. Multiple control plane nodes (API server + controller manager + scheduler)
2. An etcd cluster with quorum (3, 5, 7 members)
3. A shared control-plane endpoint via a load balancer

Even if one control plane node fails:
- The API is still reachable
- etcd retains quorum and cluster state
- Scheduler & controllers continue via leader election

---

## HA topologies
### Stacked etcd
Each control plane node also runs an etcd member; simpler setup.  
Pros: straightforward with kubeadm  
Cons: coupling of control plane + etcd can reduce isolation  
— see official docs for patterns. :contentReference[oaicite:1]{index=1}

### External etcd
etcd runs on separate dedicated nodes with its own cluster.  
Pros: better isolation & independent scaling  
Cons: higher complexity and operational burden. :contentReference[oaicite:2]{index=2}

---

## HA requirements (kubeadm context)
HA needs:
- Shared endpoint (DNS name or IP) via load balancer for kube-apiserver
- Each control plane node joins with kubeadm using `--control-plane` and a certificate key
- CNI installed before scheduling workloads
- etcd quorum maintained (odd number of members) :contentReference[oaicite:3]{index=3}

Example high-level pattern (stacked etcd):
1) Initialize first control plane with a shared endpoint
2) Join additional control plane nodes
3) Verify all API servers are registered and healthy

This ensures control plane redundancy and reduces single points of failure.

---

## Common failure scenarios (CKA)
- Single control plane → full outage if it fails
- et cd quorum loss → API instability
- Load balancer misconfiguration → unreachable control plane

---

## Minimal checklist (exam)
- Know purpose of HA control plane
- Identify control-plane components
- Understand etcd quorum and redundancy
- Know how kubeadm joins additional control plane nodes
- Know role of shared endpoint / load balancer
