# Auto Scale on Demand

## 📋 Overview

This workshop demonstrates how to implement automatic scaling in Kubernetes using Horizontal Pod Autoscalers (HPA). You'll learn how to create HPAs that automatically scale your applications based on resource metrics like CPU and memory usage. This enables your applications to handle varying loads efficiently by scaling up during high demand and scaling down during low usage periods.

## 🎯 Learning Objectives

By the end of this workshop, you will be able to:

- Understand what Horizontal Pod Autoscalers are and when to use them
- Create HPAs that scale based on resource metrics
- Configure scaling parameters (min/max replicas, target metrics)
- Monitor HPA behavior and scaling events
- Use `kubectl` commands to manage HPAs
- Understand the relationship between Deployments and HPAs

## 🏗️ Architecture

The workshop creates a complete auto-scaling setup:

**Deployment:**
- Name: `server`
- Image: `nativetechnologies/memoryapp:1.0.1`
- Initial replicas: 1
- Rolling update strategy with zero downtime
- Exposes port 8080

**Horizontal Pod Autoscaler:**
- Name: `nodejs-app-hpa`
- Target: `server` deployment
- Scaling range: 1 to 3 replicas
- Metric: Memory usage with target of 10Mi average
- Automatically scales based on memory consumption

## 📁 Files

- `hpa.yaml` - Contains both Deployment and HPA definitions

## 🚀 Getting Started

### Prerequisites

- A running Kubernetes cluster
- `kubectl` configured to communicate with your cluster
- Basic understanding of Kubernetes concepts
- Metrics server installed in your cluster (for resource metrics)

### Step 1: Verify Metrics Server

```bash
kubectl get pods -n kube-system | grep metrics-server
```

Ensure the metrics server is running for HPA to work with resource metrics.

### Step 2: Create the Deployment and HPA

```bash
kubectl apply -f ./08-auto-scale-on-demand/hpa.yaml
```

### Step 3: Verify the Resources

```bash
kubectl get deployments
kubectl get hpa
kubectl get pods
```

### Step 4: Monitor HPA Status

```bash
kubectl describe hpa nodejs-app-hpa
```

This will show you the current scaling status and metrics.

### Step 5: Generate Load to Test Scaling

```bash
# Port forward to access the application
kubectl port-forward deployment/server 8080:8080 &

# Generate load (in another terminal)
while true; do curl http://localhost:8080; sleep 1; done
```

### Step 6: Monitor Scaling Behavior

```bash
kubectl get hpa -w
kubectl get pods -w
```

Watch how the HPA scales the deployment based on memory usage.

## 🔧 Key Concepts

### HPA Components

1. **Scale Target**: The resource to scale
   - `apiVersion`: apps/v1
   - `kind`: Deployment
   - `name`: server

2. **Scaling Range**:
   - `minReplicas`: 1 (minimum number of pods)
   - `maxReplicas`: 3 (maximum number of pods)

3. **Metrics**: What to scale based on
   - `type`: Resource (CPU, memory, or custom metrics)
   - `resource.name`: memory
   - `target.type`: AverageValue
   - `target.averageValue`: 10Mi

### Scaling Behavior

- **Scale Up**: When average memory usage exceeds 10Mi
- **Scale Down**: When average memory usage drops below 10Mi
- **Cooldown**: HPA waits before scaling down to prevent thrashing
- **Stabilization**: Ensures stable scaling behavior

### Metric Types

- **Resource Metrics**: CPU and memory usage
- **Custom Metrics**: Application-specific metrics
- **External Metrics**: Metrics from external systems

## 🛠️ Useful kubectl Commands

### Monitor HPA Status
```bash
kubectl get hpa
kubectl get hpa -w
kubectl describe hpa nodejs-app-hpa
```

### Check Deployment Status
```bash
kubectl get deployments
kubectl describe deployment server
kubectl get pods -l app=server
```

### View Resource Metrics
```bash
kubectl top pods -l app=server
kubectl top nodes
```

### Check HPA Events
```bash
kubectl get events --field-selector involvedObject.name=nodejs-app-hpa
kubectl get events --field-selector involvedObject.kind=HorizontalPodAutoscaler
```

### Test Scaling Manually
```bash
# Scale deployment manually (for testing)
kubectl scale deployment server --replicas=2

# Reset to HPA control
kubectl scale deployment server --replicas=1
```

### Get HPA Configuration
```bash
kubectl get hpa nodejs-app-hpa -o yaml
```

### Monitor Pod Metrics
```bash
kubectl top pods -l app=server --containers
```

## 🔍 Troubleshooting

### Common Issues

1. **HPA not scaling**
   ```bash
   kubectl describe hpa nodejs-app-hpa
   kubectl get events --field-selector involvedObject.name=nodejs-app-hpa
   ```
   Check if metrics server is working and metrics are available.

2. **Metrics server not working**
   ```bash
   kubectl get pods -n kube-system | grep metrics-server
   kubectl logs -n kube-system deployment/metrics-server
   ```
   Verify metrics server is running and healthy.

3. **No metrics available**
   ```bash
   kubectl top pods
   kubectl top nodes
   ```
   Check if resource metrics are being collected.

4. **HPA stuck at min/max replicas**
   - Check if the target metric is being exceeded
   - Verify the scaling range is appropriate
   - Check for resource constraints

### Debugging HPA Issues

```bash
# Check HPA status and events
kubectl describe hpa nodejs-app-hpa

# Check metrics server status
kubectl get pods -n kube-system -l k8s-app=metrics-server

# Check resource usage
kubectl top pods -l app=server

# Check deployment status
kubectl describe deployment server

# Check for scaling events
kubectl get events --field-selector involvedObject.name=nodejs-app-hpa --sort-by='.lastTimestamp'
```

## 📚 Next Steps

After completing this workshop, you can:

1. Create HPAs with CPU-based scaling
2. Use custom metrics for application-specific scaling
3. Implement multi-metric HPAs
4. Configure scaling policies and behavior
5. Monitor and optimize HPA performance
6. Implement vertical pod autoscaling (VPA)

## 🔗 Related Workshops

- **04-dynamic-pvc**: Scale applications with persistent storage
- **06-one-time-task-with-job**: Use Jobs with auto-scaling
- **09-permission-controls-with-serviceaccount**: Secure auto-scaling deployments

## 🧹 Cleanup

To clean up the resources created in this workshop:

```bash
kubectl delete -f ./08-auto-scale-on-demand/hpa.yaml

# Stop port forwarding if still running
pkill -f "kubectl port-forward"
```

## ⚠️ Important Notes

- HPA requires metrics server to be installed and running
- Resource metrics (CPU/memory) are the most common scaling metrics
- Set appropriate min/max replicas to prevent resource exhaustion
- Monitor scaling behavior to optimize performance
- Consider cooldown periods to prevent scaling thrashing
- HPAs work best with stateless applications
- Test scaling behavior under realistic load conditions

---

**Happy learning! 🚀** 