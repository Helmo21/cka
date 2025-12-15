# PriorityClasses
kubectl get priorityclass
kubectl create priorityclass <name> --value=<int>

# Pods / Deployments avec priority
# → spec.priorityClassName: <name>

# Observer la préemption
kubectl get pods -w
kubectl describe pod <name>
kubectl get events --sort-by=.lastTimestamp
