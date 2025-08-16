# Kubernetes Course 3

### 🧭 Workshop Modules

1. **[Headless Service for Websocket](00-headless-service-for-ws/README.md)**
   - Learn how to create headless services for WebSocket applications
   - Understand DNS resolution for stateful applications
   - Configure services for direct pod-to-pod communication

2. **[Local Volume with Storage Class](01-localvolume-with-storageclass/README.md)**
   - Create and configure Storage Classes for local volumes
   - Understand volume binding modes and reclaim policies
   - Set up default storage classes for your cluster

3. **[Persistent Volume](02-persistent-volume/README.md)**
   - Create and manage Persistent Volumes (PVs)
   - Configure access modes and storage capacity
   - Understand the relationship between PVs and PVCs

4. **[Persistent Volume Claims](03-persistent-volume-claims/README.md)**
   - Create Persistent Volume Claims (PVCs) to request storage
   - Understand the binding process between PVCs and PVs
   - Monitor storage lifecycle and resource management

5. **[Dynamic Persistent Volume](04-dynamic-pv/README.md)**
   - Use dynamic provisioning with Storage Classes
   - Automatically create PVs based on PVC requests
   - Deploy applications with persistent storage

6. **[Claim the Persistent Volume](05-claim-the-pv/README.md)**
   - Understand PV selector mechanisms
   - Configure PVCs to bind to specific PVs
   - Monitor binding priority and selection criteria

7. **[One-Time Task with Job](06-one-time-task-with-job/README.md)**
   - Create Kubernetes Jobs for one-time tasks
   - Understand the difference between Jobs and Deployments
   - Monitor job execution and handle failures

8. **[Schedule Job with CronJob](07-schedule-job-with-cronjob/README.md)**
   - Create CronJobs for scheduled tasks
   - Configure cron expressions for timing
   - Manage job history and execution monitoring

9. **[Auto Scale on Demand](08-auto-scale-on-demand/README.md)**
   - Implement Horizontal Pod Autoscalers (HPA)
   - Scale applications based on resource metrics
   - Configure automatic scaling parameters

10. **[Permission Controls with ServiceAccount](09-permission-controls-with-serviceaccount/README.md)**
    - Create ServiceAccounts for application authentication
    - Configure RBAC (Role-Based Access Control)
    - Manage permissions and security policies

## ✅ What You'll Learn

By completing all modules, you will also learn:

- How to manage persistent storage in Kubernetes
- How to implement dynamic storage provisioning
- How to create and manage scheduled tasks
- How to implement automatic scaling for applications
- How to configure security and permissions
- How to troubleshoot storage and scaling issues
- How to monitor resource usage and performance
- How to implement best practices for production deployments

Happy hacking! 🚀