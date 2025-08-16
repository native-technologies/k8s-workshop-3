# One-Time Task with Job

## 📋 Overview

This workshop demonstrates how to use Kubernetes Jobs to run one-time tasks and batch processes. You'll learn how to create Jobs that execute a specific task, complete, and then terminate. Jobs are perfect for batch processing, data migration, backup operations, and any task that needs to run once and then finish.

## 🎯 Learning Objectives

By the end of this workshop, you will be able to:

- Understand what Kubernetes Jobs are and when to use them
- Create Jobs for one-time tasks and batch processing
- Configure Job parameters like backoff limits and restart policies
- Monitor Job execution and completion status
- Use `kubectl` commands to manage Jobs
- Understand the difference between Jobs and Deployments

## 🏗️ Architecture

The workshop creates a simple Job that:

**Job Configuration:**
- Name: `nodejs-console-log-job`
- Runs a Node.js container with Alpine Linux
- Executes a simple command to log a random number
- Uses `Never` restart policy (Job won't restart on completion)
- Sets backoff limit to 4 (maximum retry attempts)

**Container Details:**
- Image: `node:24-alpine` (lightweight Node.js runtime)
- Command: `node -e "console.log(Math.random())"`
- Purpose: Demonstrates a simple one-time task execution


## 🚀 Getting Started

### Prerequisites

- A running Kubernetes cluster
- `kubectl` configured to communicate with your cluster
- Basic understanding of Kubernetes concepts
- Completion of previous workshops (recommended)

### Step 1: Create the Job

```bash
kubectl apply -f ./06-one-time-task-with-job/job.yaml
```

### Step 2: Monitor Job Execution

```bash
kubectl get jobs
kubectl get pods
```

Watch how the Job creates a pod, executes the task, and then the pod completes.

### Step 3: Check Job Status

```bash
kubectl describe job nodejs-console-log-job
```

This will show you detailed information about the Job's execution.

### Step 4: View Job Output

```bash
kubectl logs job/nodejs-console-log-job
```

This will show the output from the completed Job.

## 🔧 Key Concepts

### Job vs Deployment

**Jobs:**
- Run to completion and then stop
- Perfect for one-time tasks
- Can have multiple completions (parallelism)
- Support retry mechanisms

**Deployments:**
- Run continuously
- Restart automatically if they fail
- Maintain desired number of replicas
- For long-running applications

### Job Components

1. **Restart Policy**: `Never`
   - `Never`: Job won't restart after completion
   - `OnFailure`: Job will restart only on failure
   - `Always`: Job will always restart (not recommended for Jobs)

2. **Backoff Limit**: `4`
   - Maximum number of retry attempts
   - Prevents infinite retry loops
   - Job fails permanently after exceeding limit

3. **Completions**: Default 1
   - Number of successful completions required
   - Can be set to run multiple instances

## 🛠️ Useful kubectl Commands

### Monitor Job Status
```bash
kubectl get jobs
kubectl get jobs -w
```

### Check Job Details
```bash
kubectl describe job nodejs-console-log-job
```

### View Job Logs
```bash
kubectl logs job/nodejs-console-log-job
```

### Check Pod Status
```bash
kubectl get pods -l job-name=nodejs-console-log-job
```

### Delete Job
```bash
kubectl delete job nodejs-console-log-job
```

### Get Job in YAML Format
```bash
kubectl get job nodejs-console-log-job -o yaml
```

### Check Job Events
```bash
kubectl get events --field-selector involvedObject.name=nodejs-console-log-job
```

### Monitor Pod Creation and Completion
```bash
kubectl get pods -w
```

## 🔍 Troubleshooting

### Common Issues

1. **Job stuck in Pending**
   ```bash
   kubectl describe job nodejs-console-log-job
   kubectl describe pod -l job-name=nodejs-console-log-job
   ```
   Check for resource constraints or image pull issues.

2. **Job keeps restarting**
   ```bash
   kubectl get pods -l job-name=nodejs-console-log-job
   ```
   Check if backoff limit is exceeded or if there are application errors.

3. **Job completed but no output**
   ```bash
   kubectl logs job/nodejs-console-log-job
   kubectl logs -l job-name=nodejs-console-log-job
   ```
   Check logs from the completed pod.

4. **Permission issues**
   ```bash
   kubectl auth can-i create job
   ```

### Debugging Job Issues

```bash
# Check Job status and events
kubectl describe job nodejs-console-log-job

# Check pod logs
kubectl logs -l job-name=nodejs-console-log-job

# Check pod events
kubectl get events --field-selector involvedObject.name=nodejs-console-log-job --sort-by='.lastTimestamp'

# Check if pods are being created
kubectl get pods -l job-name=nodejs-console-log-job
```

## 📚 Next Steps

After completing this workshop, you can:

1. Create Jobs with multiple completions (parallelism)
2. Use Jobs for data processing tasks
3. Implement Job dependencies with init containers
4. Create CronJobs for scheduled tasks
5. Monitor Job performance and resource usage
6. Implement Job cleanup policies

## 🧹 Cleanup

To clean up the resources created in this workshop:

```bash
kubectl delete -f ./06-one-time-task-with-job/job.yaml
```

**Note**: Jobs and their completed pods will be deleted. Use `--cascade=false` to keep pods.

## ⚠️ Important Notes

- Jobs are designed for one-time tasks, not continuous workloads
- Completed Jobs and their pods consume cluster resources until deleted
- Consider implementing cleanup policies for production Jobs
- Jobs with `Never` restart policy won't restart after successful completion
- Backoff limits prevent infinite retry loops

---

**Happy learning! 🚀** 