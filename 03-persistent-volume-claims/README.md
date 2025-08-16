# Persistent Volume Claims

## 📋 Overview

This workshop demonstrates how to create and manage Persistent Volume Claims (PVCs) in Kubernetes. You'll learn how to request storage resources from the cluster and understand the binding process between PVCs and Persistent Volumes (PVs). This workshop includes both a PV and PVC to show the complete storage lifecycle.

## 🎯 Learning Objectives

By the end of this workshop, you will be able to:

- Understand what a Persistent Volume Claim is and its purpose
- Create a Persistent Volume Claim to request storage
- Understand the binding process between PVCs and PVs
- Configure access modes and storage requests
- Use `kubectl` commands to manage PVCs
- Monitor the lifecycle of storage resources

## 🏗️ Architecture

The workshop creates both a Persistent Volume and a Persistent Volume Claim:

**Persistent Volume:**
- Uses `hostPath` storage type
- Provides 1Gi of storage capacity
- Supports `ReadWriteOnce` access mode
- Implements `Delete` reclaim policy
- No storage class specified (static provisioning)

**Persistent Volume Claim:**
- Requests 500Mi of storage
- Matches the PV's access mode (`ReadWriteOnce`)
- No storage class specified (binds to available PVs)
- Will automatically bind to the matching PV

## 🚀 Getting Started

### Prerequisites

- A running Kubernetes cluster
- `kubectl` configured to communicate with your cluster
- Basic understanding of Kubernetes concepts
- Completion of the Storage Class and Persistent Volume workshops (recommended)

### Step 1: Create the PV and PVC

```bash
kubectl apply -f ./03-persistent-volume-claims/pvc.yaml
```

### Step 2: Verify the Resources

```bash
kubectl get pv
kubectl get pvc
```

You should see:
- PV `pv` with status `Bound`
- PVC `pvc` with status `Bound`

### Step 3: Check the Binding

```bash
kubectl describe pv pv
kubectl describe pvc pvc
```

Notice how the PVC shows which PV it's bound to, and the PV shows which PVC claimed it.

## 🔧 Key Concepts

### Persistent Volume Claim Components

1. **Access Modes**: `ReadWriteOnce`
   - Must match the PV's access mode
   - Determines how the volume can be mounted

2. **Storage Request**: `500Mi`
   - Amount of storage requested
   - Must be less than or equal to PV capacity
   - Can be smaller than PV capacity (partial binding)

3. **Storage Class**: `""` (empty)
   - No storage class specified
   - Will bind to any available PV that matches requirements
   - For static provisioning

### Binding Process

1. **PVC Creation**: PVC is created with specific requirements
2. **PV Matching**: Kubernetes finds PVs that match:
   - Access modes
   - Storage capacity
   - Storage class (if specified)
3. **Binding**: PVC is bound to the best matching PV
4. **Status Update**: Both PV and PVC show `Bound` status

## 🛠️ Useful kubectl Commands

### List PVCs and PVs
```bash
kubectl get pvc
kubectl get pv
```

### Describe PVC
```bash
kubectl describe pvc pvc
```

### Delete PVC
```bash
kubectl delete pvc pvc
```

### Check PVC Status
```bash
kubectl get pvc -o wide
```

### Filter PVCs by Status
```bash
kubectl get pvc --field-selector status.phase=Pending
kubectl get pvc --field-selector status.phase=Bound
```

### Get PVC in YAML Format
```bash
kubectl get pvc pvc -o yaml
```

### Watch PVC Events
```bash
kubectl get events --field-selector involvedObject.name=pvc
```

## 🔍 Troubleshooting

### Common Issues

1. **PVC stuck in Pending**
   ```bash
   kubectl describe pvc pvc
   ```
   Check if there are available PVs that match the requirements.

2. **No matching PV found**
   - Verify access modes match between PV and PVC
   - Check storage capacity requirements
   - Ensure storage class compatibility

3. **PVC bound to wrong PV**
   ```bash
   kubectl get pv --field-selector status=Available
   ```
   Check if there are multiple matching PVs.

4. **Permission issues**
   ```bash
   kubectl auth can-i create persistentvolumeclaim
   ```

### Debugging Binding Issues

```bash
# Check all PVs and their status
kubectl get pv -o wide

# Check PVC details
kubectl describe pvc pvc

# Check events related to PVC
kubectl get events --field-selector involvedObject.name=pvc --sort-by='.lastTimestamp'
```

## 📚 Next Steps

After completing this workshop, you can:

1. Deploy applications that use the PVC
2. Create multiple PVCs with different requirements
3. Explore dynamic provisioning with Storage Classes
4. Learn about volume snapshots and cloning
5. Understand storage quotas and limits

## 🧹 Cleanup

To clean up the resources created in this workshop:

```bash
kubectl delete -f ./03-persistent-volume-claims/pvc.yaml
```

**Note**: The PVC will be deleted first, then the PV (due to the Delete reclaim policy).

## ⚠️ Important Notes

- PVCs must be deleted before PVs (unless using Delete reclaim policy)
- Storage requests cannot exceed PV capacity
- Access modes must match between PV and PVC
- Empty storage class means static provisioning only

---

**Happy learning! 🚀** 