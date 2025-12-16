# RBAC (Role-Based Access Control) (CKA)

## What CKA tests
The CKA tests whether you can:
- Define Roles and ClusterRoles
- Bind them with RoleBinding and ClusterRoleBinding
- Use `kubectl auth can-i` to check permissions
- Fix permission errors on resources

RBAC is fundamental to secure multi-user clusters.

---

## Mental model (must be automatic)
RBAC has 3 parts:
1) **Who**: User or ServiceAccount
2) **Can do what**: Role (namespaced) or ClusterRole (cluster-wide)
3) **Where/When**: Bound via RoleBinding or ClusterRoleBinding

Kubectl uses these rules to accept or reject API operations.

---

## Commands (cheat)

Check identity:

kubectl config view
kubectl config current-context
kubectl config get-contexts

## Check permissions:

kubectl auth can-i <verb> <resource>
kubectl auth can-i <verb> <resource> --as <user> -n <ns>

## Create RBAC resources: 

kubectl create role ...
kubectl create clusterrole ...
kubectl create rolebinding ...
kubectl create clusterrolebinding ...





# Case 1: Forbidden errors (403)

Cause:

    No appropriate Role/ClusterRole

    No binding to the ServiceAccount/user

Fix:

    Create Role + RoleBinding or ClusterRole + ClusterRoleBinding

# Case 2: Too broad permissions

Cause:

    Using cluster-admin unnecessarily

Fix:

Use least privilege

# Case 3: Namespace vs cluster scope mismatch

Cause:

    Role bound cluster-wide or incorrectly scoped

Fix:

    Align Role/Binding scope (namespace vs cluster)