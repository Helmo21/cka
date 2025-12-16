
## What CKA tests
The CKA tests whether you can:
- Create NetworkPolicy to restrict traffic
- Understand how policies select pods
- Define ingress and egress rules
- Debug connectivity issues

NetworkPolicy is about **pod-to-pod traffic rules**, not service routing.

---

## Mental model (must be automatic)
By default:
- Pods are **non-isolated** → all inbound/outbound allowed
- Applying a NetworkPolicy may isolate traffic
- Policies use **selectors** to choose which pods are affected

Kubernetes only enforces NetworkPolicy if the **CNI plugin supports it**. :contentReference[oaicite:4]{index=4}

---

## Commands (cheat)

List policies:

kubectl get networkpolicy 

Describe:

kubectl describe networkpolicy <name>

Debug connectivity:

kubectl exec -it <pod> -- ping <other pod>
kubectl exec -it <pod> -- curl <service>

Common failure scenarios
Case 1: Policy blocks all ingress

Symptoms:

Application unreachable
Cause:

PodSelector matches wrong pods
Fix:

Adjust selector

Split policies by namespace if needed

Case 2: No egress allowed

Symptoms:

Pods can’t reach external services
Cause:

Egress rule too restrictive
Fix:

Add allowed CIDR or pod selector

Case 3: Policy not enforced

Symptoms:

Changes have no effect
Cause:

CNI plugin does not support NetworkPolicy
Fix:

Choose a supporting CNI
