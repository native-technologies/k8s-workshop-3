# Dynamic Persistent Volume

## 📋 Overview

This workshop demonstrates how to use dynamic provisioning with Persistent Volume (PV) in Kubernetes. You'll learn how to create PV that automatically provision storage using Storage Classes, and deploy applications that use this dynamically created storage. This approach eliminates the need to manually create Persistent Volumes.

## 🎯 Learning Objectives

By the end of this workshop, you will be able to:

- Understand dynamic provisioning and its benefits
- Create PVCs that use Storage Classes for automatic provisioning
- Deploy applications with persistent storage
- Monitor the dynamic creation of PVs
- Use `kubectl` commands to manage dynamically provisioned storage
- Understand the complete storage lifecycle with dynamic provisioning

## 🏗️ Architecture

The workshop creates a complete application deployment with dynamic storage:

**Persistent Volume Claim:**
- Uses `local-path` Storage Class for dynamic provisioning
- Requests 100Mi of storage
- Supports `ReadWriteOnce` access mode
- Will automatically trigger PV creation

**Deployment:**
- Runs a sample API server application
- Mounts the dynamically created storage at `/data`
- Uses the `docker.io/nativetechnologies/orderapp:1.1.0` image
- Single replica for demonstration

## 🚀 Getting Started

### Prerequisites

- A running Kubernetes cluster
- `kubectl` configured to communicate with your cluster
- Basic understanding of Kubernetes concepts
- Completion of the Storage Class workshop (required)
- A Storage Class named `local-path` available in your cluster

### Step 1: Verify Storage Class Availability

```bash
kubectl get sc
```

Ensure you see a Storage Class named `local-path` in the list.

### Step 2: Create the PVC and Deployment

```bash
kubectl apply -f ./04-dynamic-pv/deployment.yaml
```

### Step 3: Monitor the Dynamic Provisioning

```bash
kubectl get pvc
kubectl get pv
```

Watch how the PVC status changes from `Pending` to `Bound` and a new PV is automatically created.

### Step 4: Check the Application Deployment

```bash
kubectl get pods
kubectl get deployments
```

Verify that the API server pod is running and using the persistent storage.

## 🔧 Key Concepts

### Dynamic Provisioning Process

1. **PVC Creation**: PVC is created with a Storage Class reference
2. **Storage Class Lookup**: Kubernetes finds the specified Storage Class
3. **Provisioner Action**: The Storage Class's provisioner creates a new PV
4. **Automatic Binding**: PVC is automatically bound to the newly created PV
5. **Application Deployment**: Pod can mount the storage and start using it

### Benefits of Dynamic Provisioning

- **No Manual PV Management**: PVs are created automatically
- **Scalability**: Easy to create multiple PVCs without manual intervention
- **Flexibility**: Different Storage Classes for different use cases
- **Resource Efficiency**: Storage is provisioned only when needed

### Storage Class Integration

- **`local-path`**: Uses local storage on the node
- **Automatic Provisioning**: No manual PV creation required
- **Lifecycle Management**: PVs are managed by the Storage Class

## 🛠️ Useful kubectl Commands

### Monitor PVC and PV Creation
```bash
kubectl get pvc -w
kubectl get pv -w
```

### Check Application Status
```bash
kubectl get pods -l app=api-server
kubectl get deployments api-server
```

### Describe Resources
```bash
kubectl describe pvc api-server-pvc
kubectl describe pv
kubectl describe pod -l app=api-server
```

### Check Storage Usage
```bash
kubectl exec -it $(kubectl get pod -l app=api-server -o jsonpath='{.items[0].metadata.name}') -- df -h /data
```

### View Application Logs
```bash
kubectl logs -l app=api-server
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
   Check if the Storage Class exists and is working properly.

2. **Storage Class not found**
   ```bash
   kubectl get sc local-path
   ```
   Ensure the `local-path` Storage Class is available in your cluster.

3. **Pod not starting**
   ```bash
   kubectl describe pod -l app=api-server
   ```
   Check if there are issues with volume mounting or image pulling.

4. **Permission issues**
   ```bash
   kubectl auth can-i create persistentvolumeclaim
   kubectl auth can-i create deployment
   ```

### Debugging Dynamic Provisioning

```bash
# Check Storage Class details
kubectl describe sc local-path

# Check PVC events
kubectl get events --field-selector involvedObject.name=api-server-pvc --sort-by='.lastTimestamp'

# Check if provisioner is working
kubectl get events --field-selector reason=Provisioning
```

## 📚 Next Steps

After completing this workshop, you can:

1. Scale the deployment to multiple replicas
2. Create multiple PVCs with different Storage Classes
3. Explore different storage provisioners (AWS EBS, GCP PD, etc.)
4. Learn about volume snapshots and cloning
5. Implement storage quotas and limits
6. Monitor storage usage and performance

## 🧹 Cleanup

To clean up the resources created in this workshop:

```bash
kubectl delete -f ./04-dynamic-pv/deployment.yaml
```

**Note**: The PVC and associated PV will be deleted automatically.

## ⚠️ Important Notes

- Dynamic provisioning requires a working Storage Class
- PVs created dynamically are typically deleted when PVC is deleted
- Storage Class provisioners must be available in your cluster
- Local storage is node-specific and not suitable for multi-node deployments

---

**Happy learning! 🚀** 