# List api cdrs 

kubectl get crds 

# CRDs
kubectl get crds
kubectl get crds | grep <name>

# Custom Resources
kubectl get <crd-name>
kubectl describe <crd-name>
kubectl edit <crd-name>
kubectl get <crd-name> -o yaml

# Operator Pods
kubectl get pods -n <ns>
kubectl logs -n <ns> -l <label>

# Resilience test
kubectl delete pod <name>
kubectl get pods -w

