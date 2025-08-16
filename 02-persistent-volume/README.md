# Persistent Volume

## 📋 Overview

This workshop demonstrates how to create and manage Persistent Volumes (PVs) in Kubernetes. You'll learn how to define storage resources that can be claimed by Persistent Volume Claims (PVCs) and understand the different access modes and reclaim policies available.

## 🎯 Learning Objectives

By the end of this workshop, you will be able to:

- Understand what a Persistent Volume is and its purpose
- Create a Persistent Volume using hostPath storage
- Configure access modes for different use cases
- Set appropriate reclaim policies
- Use `kubectl` commands to manage Persistent Volumes
- Understand the relationship between PVs and PVCs

## 🏗️ Architecture

The workshop creates a Persistent Volume that:
- Uses `hostPath` storage type (local node storage)
- Provides 1Gi of storage capacity
- Supports `ReadWriteOnce` access mode
- Implements `Retain` reclaim policy to preserve data
- Mounts to `/tmp/data` on the host node

## 🚀 Getting Started

### Prerequisites

- A running Kubernetes cluster
- `kubectl` configured to communicate with your cluster
- Basic understanding of Kubernetes concepts
- Completion of the Storage Class workshop (recommended)

### Step 1: Create the Persistent Volume

```bash
kubectl apply -f ./02-persistent-volume/pv.yaml
```

### Step 2: Verify the Persistent Volume

```bash
kubectl get pv
```

You should see your `pv` Persistent Volume listed with status `Available`.

### Step 3: Check Persistent Volume Details

```bash
kubectl describe pv pv
```

This will show you the detailed configuration including:
- Storage capacity
- Access modes
- Reclaim policy
- Storage class
- Host path location

## 🔧 Key Concepts

### Persistent Volume Components

1. **Capacity**: `1Gi`
   - Defines the amount of storage available
   - Must match PVC requests

2. **Access Modes**: `ReadWriteOnce`
   - `ReadWriteOnce`: Can be mounted as read-write by a single node
   - `ReadOnlyMany`: Can be mounted as read-only by multiple nodes
   - `ReadWriteMany`: Can be mounted as read-write by multiple nodes
   - `ReadWriteOncePod`: Can be mounted as read-write by a single pod

3. **Reclaim Policy**: `Retain`
   - `Retain`: Data is preserved when PVC is deleted
   - `Recycle`: Data is deleted and volume is made available again
   - `Delete`: Volume is deleted when PVC is deleted

4. **Storage Type**: `hostPath`
   - Uses local storage on the node
   - Data persists on the host filesystem
   - Useful for development and testing

## 🛠️ Useful kubectl Commands

### List Persistent Volumes
```bash
kubectl get pv
kubectl get persistentvolume
```

### Describe Persistent Volume
```bash
kubectl describe pv pv
```

### Delete Persistent Volume
```bash
kubectl delete pv pv
```

### Check PV Status
```bash
kubectl get pv -o wide
```

### Filter PVs by Status
```bash
kubectl get pv --field-selector status=Available
kubectl get pv --field-selector status=Bound
```

### Get PV in YAML Format
```bash
kubectl get pv pv -o yaml
```

## 🔍 Troubleshooting

### Common Issues

1. **PV stuck in Pending**
   ```bash
   kubectl describe pv pv
   ```
   Check events for binding issues.

2. **Access denied on hostPath**
   - Ensure the host path `/tmp/data` exists and has proper permissions
   - Check if the node has sufficient storage

3. **PV not binding to PVC**
   - Verify access modes match between PV and PVC
   - Check storage capacity requirements
   - Ensure storage class compatibility

4. **Permission issues**
   ```bash
   kubectl auth can-i create persistentvolume
   ```

## 📚 Next Steps

After completing this workshop, you can:

1. Create Persistent Volume Claims to bind to this PV
2. Deploy applications that use the persistent storage
3. Explore other storage types (NFS, iSCSI, etc.)
4. Learn about dynamic provisioning with Storage Classes
5. Understand volume snapshots and cloning

## 🧹 Cleanup

To clean up the resources created in this workshop:

```bash
kubectl delete -f ./02-persistent-volume/pv.yaml
```

**Note**: If the PV is bound to a PVC, you'll need to delete the PVC first.

---

**Happy learning! 🚀** 