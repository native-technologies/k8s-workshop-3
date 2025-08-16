# Permission Controls with ServiceAccount

## 📋 Overview

This workshop demonstrates how to implement fine-grained permission controls in Kubernetes using ServiceAccounts, Roles, and RoleBindings. You'll learn how to create ServiceAccounts with specific permissions, define custom roles, and bind them together to control access to cluster resources. This is essential for implementing the principle of least privilege and securing your Kubernetes applications.

## 🎯 Learning Objectives

By the end of this workshop, you will be able to:

- Understand ServiceAccounts, Roles, and RoleBindings
- Create ServiceAccounts for applications
- Define custom roles with specific permissions
- Bind roles to ServiceAccounts using RoleBindings
- Export and use ServiceAccount credentials
- Use `kubectl` commands to manage RBAC resources
- Implement least privilege access controls

## 🏗️ Architecture

The workshop creates a complete RBAC setup:

**ServiceAccount:**
- Name: `pod-reader`
- Purpose: Service account for applications that need to read pod information

**Role:**
- Name: `pod-read-role`
- Permissions: Read access to pods (`get`, `list`, `watch`)
- Scope: Namespace-level permissions

**RoleBinding:**
- Name: `pod-read-rolebinding`
- Binds: `pod-read-role` to `pod-reader` ServiceAccount
- Purpose: Grants the ServiceAccount the defined permissions


## 🚀 Getting Started

### Prerequisites

- A running Kubernetes cluster
- `kubectl` configured to communicate with your cluster
- Basic understanding of Kubernetes concepts
- Cluster admin permissions (for creating RBAC resources)

### Step 1: Create the RBAC Resources

```bash
kubectl apply -f ./09-permission-controls-with-serviceaccount/serviceAccount.yaml
```

### Step 2: Verify the Resources

```bash
kubectl get serviceaccount pod-reader
kubectl get role pod-read-role
kubectl get rolebinding pod-read-rolebinding
```

### Step 3: Export ServiceAccount Credentials

Get the token, cluster CA and API server URL.
```bash
# Get the ServiceAccount token
TOKEN=$(kubectl get secret pod-reader-token -o jsonpath='{.data.token}' | base64 --decode)
# Get the cluster name
CLUSTER_NAME=$(kubectl config view --minify -o jsonpath='{.clusters[0].name}')
# Get the cluster CA certificate
CLUSTER_CA=$(kubectl get secret pod-reader-token -o jsonpath='{.data.ca\.crt}' | base64 --decode)
# Get the cluster endpoint
SERVER=$(kubectl config view --minify -o jsonpath='{.clusters[0].cluster.server}')
```

You can construct the kubeconfig file by follows below format:

```yaml
apiVersion: v1
kind: Config
clusters:
- cluster:
    certificate-authority-data: $(kubectl get secret pod-reader-token -o jsonpath='{.data.ca\.crt}')
    server: ${SERVER}
  name: ${CLUSTER_NAME}
contexts:
- context:
    cluster: ${CLUSTER_NAME}
    namespace: default
    user: pod-reader
  name: pod-reader-context
current-context: pod-reader-context
users:
- name: pod-reader
  user:
    token: ${TOKEN}
```

### Step 4: Test the Permissions

```bash
# Test using the ServiceAccount token
TOKEN=$(cat pod-reader-token.txt)
CLUSTER_ENDPOINT=$(cat cluster-endpoint.txt)

# Test pod listing permission
curl -H "Authorization: Bearer $TOKEN" \
  --cacert pod-reader-ca.crt \
  "$CLUSTER_ENDPOINT/api/v1/namespaces/default/pods"
```

### Step 5: Verify Role Permissions

```bash
kubectl auth can-i get pods --as=system:serviceaccount:default:pod-reader
kubectl auth can-i list pods --as=system:serviceaccount:default:pod-reader
kubectl auth can-i watch pods --as=system:serviceaccount:default:pod-reader
kubectl auth can-i create pods --as=system:serviceaccount:default:pod-reader
```

## 🔧 Key Concepts

### RBAC Components

1. **ServiceAccount**: Identity for applications
   - Represents an application or service
   - Can be assigned specific permissions
   - Automatically gets a token for authentication

2. **Role**: Defines permissions
   - Namespace-scoped permissions
   - Specifies resources and allowed verbs
   - Can be custom or built-in

3. **RoleBinding**: Connects roles to subjects
   - Binds roles to ServiceAccounts, users, or groups
   - Grants the defined permissions
   - Namespace-scoped

### Permission Verbs

- `get`: Read individual resources
- `list`: List multiple resources
- `watch`: Watch for resource changes
- `create`: Create new resources
- `update`: Modify existing resources
- `patch`: Partially update resources
- `delete`: Remove resources

### Resource Types

- `pods`: Pod resources
- `services`: Service resources
- `configmaps`: ConfigMap resources
- `secrets`: Secret resources
- `deployments`: Deployment resources

## 🛠️ Useful kubectl Commands

### Manage ServiceAccounts
```bash
kubectl get serviceaccounts
kubectl describe serviceaccount pod-reader
kubectl delete serviceaccount pod-reader
```

### Manage Roles
```bash
kubectl get roles
kubectl describe role pod-read-role
kubectl get role pod-read-role -o yaml
```

### Manage RoleBindings
```bash
kubectl get rolebindings
kubectl describe rolebinding pod-read-rolebinding
kubectl get rolebinding pod-read-rolebinding -o yaml
```

### Test Permissions
```bash
kubectl auth can-i get pods --as=system:serviceaccount:default:pod-reader
kubectl auth can-i list pods --as=system:serviceaccount:default:pod-reader
kubectl auth can-i create pods --as=system:serviceaccount:default:pod-reader
```

### Export Credentials
```bash
# Export token
kubectl get secret $(kubectl get serviceaccount pod-reader -o jsonpath='{.secrets[0].name}') -o jsonpath='{.data.token}' | base64 --decode

# Export CA certificate
kubectl get secret $(kubectl get serviceaccount pod-reader -o jsonpath='{.secrets[0].name}') -o jsonpath='{.data.ca\.crt}' | base64 --decode

# Get cluster endpoint
kubectl config view --minify -o jsonpath='{.clusters[0].cluster.server}'
```

### Check RBAC Resources
```bash
kubectl get all -o yaml | grep -A 10 -B 10 "kind: ServiceAccount"
kubectl get all -o yaml | grep -A 10 -B 10 "kind: Role"
kubectl get all -o yaml | grep -A 10 -B 10 "kind: RoleBinding"
```

## 🔍 Troubleshooting

### Common Issues

1. **ServiceAccount not found**
   ```bash
   kubectl get serviceaccount pod-reader
   kubectl describe serviceaccount pod-reader
   ```
   Check if the ServiceAccount was created successfully.

2. **Permission denied errors**
   ```bash
   kubectl auth can-i get pods --as=system:serviceaccount:default:pod-reader
   ```
   Verify that the RoleBinding is correctly configured.

3. **Token not working**
   ```bash
   kubectl get secret $(kubectl get serviceaccount pod-reader -o jsonpath='{.secrets[0].name}')
   ```
   Check if the ServiceAccount has a valid token secret.

4. **RoleBinding not working**
   ```bash
   kubectl describe rolebinding pod-read-rolebinding
   ```
   Verify the roleRef and subjects are correctly configured.

### Debugging RBAC Issues

```bash
# Check ServiceAccount details
kubectl describe serviceaccount pod-reader

# Check Role permissions
kubectl describe role pod-read-role

# Check RoleBinding configuration
kubectl describe rolebinding pod-read-rolebinding

# Test permissions with kubectl
kubectl auth can-i get pods --as=system:serviceaccount:default:pod-reader

# Check for RBAC events
kubectl get events --field-selector reason=Forbidden
```

## 📚 Next Steps

After completing this workshop, you can:

1. Create ServiceAccounts for different applications
2. Define more complex roles with multiple resources
3. Use ClusterRoles and ClusterRoleBindings for cluster-wide permissions
4. Implement namespace isolation with RBAC
5. Monitor and audit RBAC usage
6. Implement least privilege access controls


## 🧹 Cleanup

To clean up the resources created in this workshop:

```bash
kubectl delete -f ./09-permission-controls-with-serviceaccount/serviceAccount.yaml
```

## ⚠️ Important Notes

- ServiceAccounts are namespace-scoped by default
- Roles and RoleBindings are also namespace-scoped
- Use ClusterRoles and ClusterRoleBindings for cluster-wide permissions
- Always follow the principle of least privilege
- Regularly audit and review RBAC configurations
- ServiceAccount tokens should be kept secure and not shared
- Consider using external identity providers for production environments

---

**Happy learning! 🚀** 