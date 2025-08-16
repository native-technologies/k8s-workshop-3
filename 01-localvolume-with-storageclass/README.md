# Local Volume with Storage Class

## 📋 Overview

This workshop demonstrates how to create and use a Storage Class for local volumes in Kubernetes. You'll learn how to define a Storage Class that uses local storage provisioners and how to bind Persistent Volume Claims to local storage resources.

## 🎯 Learning Objectives

By the end of this workshop, you will be able to:

- Understand what a Storage Class is and its purpose
- Create a Storage Class for local storage
- Configure volume binding modes
- Set appropriate reclaim policies
- Use `kubectl` commands to manage Storage Classes

## 🏗️ Architecture

The workshop uses a Storage Class that:
- Uses the `kubernetes.io/no-provisioner` provisioner (for local volumes)
- Implements `WaitForFirstConsumer` binding mode
- Sets `Retain` reclaim policy to preserve data

## 🚀 Getting Started

### Prerequisites

- A running Kubernetes cluster
- `kubectl` configured to communicate with your cluster
- Basic understanding of Kubernetes concepts

### Step 1: Create the Storage Class

```bash
kubectl apply -f ./01-localvolume-with-storageclass/storageClass.yaml
```

### Step 2: Verify the Storage Class

```bash
kubectl get sc
```

You should see your `local-storage` Storage Class listed.

### Step 3: Check Storage Class Details

```bash
kubectl describe sc local-storage
```

This will show you the detailed configuration including:
- Provisioner type
- Volume binding mode
- Reclaim policy
- Annotations

## 🔧 Key Concepts

### Storage Class Components

1. **Provisioner**: `kubernetes.io/no-provisioner`
   - Used for local volumes that are pre-created
   - No automatic provisioning occurs

2. **Volume Binding Mode**: `WaitForFirstConsumer`
   - PVC binding is delayed until a Pod using the PVC is created
   - Allows for better scheduling decisions

3. **Reclaim Policy**: `Retain`
   - Data is preserved when PVC is deleted
   - Manual cleanup required

4. **Default Class Annotation**: `storageclass.kubernetes.io/is-default-class: "true"`
   - Makes this the default Storage Class for the cluster
   - PVCs without a Storage Class specified will use this one

## 🛠️ Useful kubectl Commands

### List Storage Classes
```bash
kubectl get sc
kubectl get storageclass
```

### Describe Storage Class
```bash
kubectl describe sc local-storage
```

### Delete Storage Class
```bash
kubectl delete sc local-storage
```

### Check Default Storage Class
```bash
kubectl get sc -o jsonpath='{.items[?(@.metadata.annotations.storageclass\.kubernetes\.io/is-default-class=="true")].metadata.name}'
```

## 🔍 Troubleshooting

### Common Issues

1. **Storage Class not found**
   ```bash
   kubectl get sc
   ```
   Ensure the Storage Class was created successfully.

2. **PVC stuck in Pending**
   - Check if local volumes are available
   - Verify Storage Class configuration
   - Check events: `kubectl describe pvc <pvc-name>`

3. **Permission issues**
   ```bash
   kubectl auth can-i create storageclass
   ```

## 📚 Next Steps

After completing this workshop, you can:

1. Create Persistent Volume Claims that use this Storage Class
2. Create local Persistent Volumes to match the Storage Class
3. Deploy applications that use local storage
4. Explore other Storage Class provisioners (AWS EBS, GCP PD, etc.)

## 🧹 Cleanup

To clean up the resources created in this workshop:

```bash
kubectl delete -f ./01-localvolume-with-storageclass/storageClass.yaml
```

---

**Happy learning! 🚀**
