# Inspect
kubectl get deploy
kubectl describe deploy <name>
kubectl get rs
kubectl get pods

# Scale
kubectl scale deploy <name> --replicas=<n>

# Update image
kubectl set image deploy/<name> <container>=<image>

# Rollout control
kubectl rollout status deploy/<name>
kubectl rollout history deploy/<name>
kubectl rollout undo deploy/<name>




Case 1: Pods not created

Symptoms:

    Deployment exists

    No Pods or fewer Pods than replicas

Causes:

    Selector does not match template labels

    Invalid image

    Insufficient resources

Fix:

    Ensure spec.selector.matchLabels matches spec.template.metadata.labels

    Check events and pod status




Case 2: Pods Pending

    Symptoms:

    Pods stuck in Pending

Causes:

    Insufficient CPU/memory

    NodeSelector / affinity mismatch

    Taints without tolerations

Fix:

    Inspect pod conditions and events

    Adjust requests or scheduling constraints




Case 3: Rolling update stuck

Symptoms:

    Old pods not terminating

    New pods not becoming Ready

Causes:

    Readiness probe failing

    maxUnavailable / maxSurge constraints

Fix:

    Inspect probes

    Adjust strategy or fix application readiness



Case 4: Bad update, need rollback

Symptoms:

    New version crashes

Fix:

    Roll back to previous revision

    Verify pods become stable