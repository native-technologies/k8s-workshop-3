# Claim the Persistent Volume

## 📋 Overview

This workshop demonstrates how to use Persistent Volume Claims (PVCs) with selectors to claim specific Persistent Volumes (PVs) in Kubernetes. You'll learn how to create multiple PVs with labels and then create a PVC that uses label selectors to bind to a specific PV. This approach gives you fine-grained control over which storage resources your applications use.

## 🎯 Learning Objectives

By the end of this workshop, you will be able to:

- Understand how PV selectors work with PVCs
- Create multiple PVs with different capacities and labels
- Use label selectors in PVCs to claim specific PVs
- Control the binding process between PVCs and PVs
- Deploy applications with targeted storage resources
- Use `kubectl` commands to manage selective PV binding

## 🏗️ Architecture

The workshop creates a selective storage binding scenario:

**Persistent Volumes (3 different sizes):**
- `pv1`: 1Gi capacity, labeled `name: api-server-pv`
- `pv2`: 5Gi capacity, labeled `name: api-server-pv`
- `pv3`: 10Gi capacity, labeled `name: api-server-pv`
- All use `ReadWriteOnce` access mode
- All use `Retain` reclaim policy
- All mount to `/mnt/data/app` on host

**Persistent Volume Claim:**
- Requests 2Gi of storage
- Uses label selector `name: api-server-pv`
- Will bind to the smallest PV that meets requirements (pv2 with 5Gi)
- No storage class specified (static provisioning)

**Deployment:**
- Runs an API server application
- Mounts the claimed storage at `/data`
- Uses the `docker.io/nativetechnologies/orderapp:1.1.0` image

## 🚀 Getting Started

### Prerequisites

- A running Kubernetes cluster
- `kubectl` configured to communicate with your cluster
- Basic understanding of Kubernetes concepts
- Completion of the Storage Class and Persistent Volume workshops (recommended)

### Step 1: Create the Persistent Volumes

**Important**: You must create the PVs first before creating the PVC.

```bash
kubectl apply -f ./05-claim-the-pv/pv.yaml
```

### Step 2: Verify the PVs are Available

```bash
kubectl get pv
```

You should see three PVs (`pv1`, `pv2`, `pv3`) all with status `Available`.

### Step 3: Create the PVC and Deployment

```bash
kubectl apply -f ./05-claim-the-pv/pvc.yaml
```

### Step 4: Monitor the Binding Process

```bash
kubectl get pvc
kubectl get pv
```

Watch how the PVC binds to one of the available PVs that matches the selector.

### Step 5: Check the Application Deployment

```bash
kubectl get pods
kubectl get deployments
```

Verify that the API server pod is running and using the claimed storage.

## 🔧 Key Concepts

### PV Selector Mechanism

1. **Label Matching**: PVC uses `selector.matchLabels` to find PVs with matching labels
2. **Capacity Filtering**: Only PVs with sufficient capacity are considered
3. **Best Match Selection**: Kubernetes selects the smallest PV that meets requirements
4. **Binding**: PVC is bound to the selected PV

### Selection Criteria

- **Labels**: Must match `name: api-server-pv`
- **Access Modes**: Must support `ReadWriteOnce`
- **Capacity**: Must provide at least 2Gi (PVC request)
- **Storage Class**: Must be compatible (empty string in this case)

### Binding Priority

When multiple PVs match the criteria, Kubernetes selects:
1. Smallest capacity that meets the request
2. PV with the most specific labels
3. PV created first (if all else is equal)

## 🛠️ Useful kubectl Commands

### Monitor PV and PVC Status
```bash
kubectl get pv -w
kubectl get pvc -w
```

### Check PV Details
```bash
kubectl describe pv pv1
kubectl describe pv pv2
kubectl describe pv pv3
```

### Check PVC Binding
```bash
kubectl describe pvc api-server-pvc
```

### Filter PVs by Labels
```bash
kubectl get pv -l name=api-server-pv
```

### Check Application Status
```bash
kubectl get pods -l app=api-server
kubectl get deployments api-server
```

### View Storage Usage
```bash
kubectl exec -it $(kubectl get pod -l app=api-server -o jsonpath='{.items[0].metadata.name}') -- df -h /data
```

### Check Events
```bash
kubectl get events --field-selector involvedObject.name=api-server-pvc
```

## 🔍 Troubleshooting

### Common Issues

1. **PVC stuck in Pending**
   ```bash
   kubectl describe pvc api-server-pvc
   ```
   Check if there are PVs with matching labels and sufficient capacity.

2. **No matching PVs found**
   ```bash
   kubectl get pv -l name=api-server-pv
   ```
   Verify that PVs exist with the correct labels.

3. **Wrong PV selected**
   - Check PV capacities and labels
   - Verify PVC selector configuration
   - Review binding priority rules

4. **Permission issues**
   ```bash
   kubectl auth can-i create persistentvolume
   kubectl auth can-i create persistentvolumeclaim
   ```

### Debugging Binding Issues

```bash
# Check all PVs and their labels
kubectl get pv --show-labels

# Check PVC selector configuration
kubectl get pvc api-server-pvc -o yaml

# Check binding events
kubectl get events --field-selector involvedObject.name=api-server-pvc --sort-by='.lastTimestamp'
```

## 📚 Next Steps

After completing this workshop, you can:

1. Create multiple PVCs with different selectors
2. Use more complex label selectors with multiple labels
3. Implement storage tiering with different PV types
4. Explore volume snapshots and cloning
5. Monitor storage usage and performance
6. Implement storage quotas and limits

## 🧹 Cleanup

To clean up the resources created in this workshop:

```bash
# Delete PVC and Deployment first
kubectl delete -f ./05-claim-the-pv/pvc.yaml

# Then delete the PVs
kubectl delete -f ./05-claim-the-pv/pv.yaml
```

**Note**: PVCs must be deleted before PVs due to the Retain reclaim policy.

## ⚠️ Important Notes

- **Order matters**: Always create PVs before PVCs when using selectors
- Label selectors provide fine-grained control over PV selection
- Multiple PVs can have the same labels for redundancy
- Binding priority favors smaller PVs that meet requirements
- Selectors work only with static provisioning (no Storage Class)

---

**Happy learning! 🚀** 