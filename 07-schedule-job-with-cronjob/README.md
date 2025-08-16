# Schedule Job with CronJob

## 📋 Overview

This workshop demonstrates how to use Kubernetes CronJobs to schedule and run Jobs at specified intervals. You'll learn how to create CronJobs that automatically trigger Jobs based on cron expressions, similar to traditional cron jobs in Unix systems. CronJobs are perfect for scheduled tasks like backups, data processing, maintenance operations, and periodic reports.

## 🎯 Learning Objectives

By the end of this workshop, you will be able to:

- Understand what Kubernetes CronJobs are and when to use them
- Create CronJobs with cron expressions for scheduling
- Configure time zones and scheduling parameters
- Monitor CronJob execution and Job history
- Use `kubectl` commands to manage CronJobs
- Understand the relationship between CronJobs and Jobs

## 🏗️ Architecture

The workshop creates a CronJob that:

**CronJob Configuration:**
- Name: `hello-cronjob`
- Schedule: `"* * * * *"` (runs every minute)
- Time Zone: `Asia/Singapore`
- Failed Jobs History Limit: 1 (keep only 1 failed job)
- Successful Jobs History Limit: 1 (keep only 1 successful job)

**Job Template:**
- Container: `busybox:1.28` (lightweight Linux container)
- Command: Displays current date, waits 3 seconds, then displays date again and a hello message
- Restart Policy: `OnFailure` (restart only on failure)

## 🚀 Getting Started

### Prerequisites

- A running Kubernetes cluster
- `kubectl` configured to communicate with your cluster
- Basic understanding of Kubernetes concepts
- Completion of the One-Time Task with Job workshop (recommended)

### Step 1: Create the CronJob

```bash
kubectl apply -f ./07-schedule-job-with-cronjob/cronjob.yaml
```

### Step 2: Monitor CronJob Status

```bash
kubectl get cronjobs
kubectl get cronjobs -w
```

Watch how the CronJob creates Jobs at the scheduled intervals.

### Step 3: Check Job Creation

```bash
kubectl get jobs
kubectl get pods
```

Observe how Jobs are created and completed according to the schedule.

### Step 4: View CronJob Details

```bash
kubectl describe cronjob hello-cronjob
```

This will show you detailed information about the CronJob's configuration and status.

### Step 5: Check Job Output

```bash
kubectl logs job/hello-cronjob-<timestamp>
```

Replace `<timestamp>` with the actual job name to view the output from a specific Job.

## 🔧 Key Concepts

### Cron Expression Format

The cron expression `"* * * * *"` follows the format:
```
┌───────────── minute (0 - 59)
│ ┌───────────── hour (0 - 23)
│ │ ┌───────────── day of the month (1 - 31)
│ │ │ ┌───────────── month (1 - 12)
│ │ │ │ ┌───────────── day of the week (0 - 6) (Sunday to Saturday)
│ │ │ │ │
* * * * *
```

**Common Examples:**
- `"0 * * * *"` - Every hour at minute 0
- `"0 0 * * *"` - Every day at midnight
- `"0 0 * * 0"` - Every Sunday at midnight
- `"*/5 * * * *"` - Every 5 minutes

### CronJob Components

1. **Schedule**: `"* * * * *"`
   - Cron expression defining when Jobs should run
   - Uses standard cron syntax

2. **Time Zone**: `"Asia/Singapore"`
   - Specifies the time zone for scheduling
   - Important for accurate scheduling across regions

3. **History Limits**:
   - `failedJobsHistoryLimit: 1` - Keep only 1 failed job
   - `successfulJobsHistoryLimit: 1` - Keep only 1 successful job
   - Helps manage cluster resources

4. **Job Template**: Defines the Job specification
   - Container image and command
   - Restart policy and other Job settings

## 🛠️ Useful kubectl Commands

### Monitor CronJob Status
```bash
kubectl get cronjobs
kubectl get cronjobs -w
```

### Check CronJob Details
```bash
kubectl describe cronjob hello-cronjob
```

### List Jobs Created by CronJob
```bash
kubectl get jobs -l job-name=hello-cronjob
```

### View Job Logs
```bash
kubectl logs job/hello-cronjob-<timestamp>
```

### Suspend/Resume CronJob
```bash
kubectl patch cronjob hello-cronjob -p '{"spec" : {"suspend" : true}}'
kubectl patch cronjob hello-cronjob -p '{"spec" : {"suspend" : false}}'
```

### Delete CronJob
```bash
kubectl delete cronjob hello-cronjob
```

### Get CronJob in YAML Format
```bash
kubectl get cronjob hello-cronjob -o yaml
```

### Check CronJob Events
```bash
kubectl get events --field-selector involvedObject.name=hello-cronjob
```

### Monitor Job Creation
```bash
kubectl get jobs -w
```

## 🔍 Troubleshooting

### Common Issues

1. **CronJob not creating Jobs**
   ```bash
   kubectl describe cronjob hello-cronjob
   ```
   Check if the CronJob is suspended or has scheduling issues.

2. **Jobs failing repeatedly**
   ```bash
   kubectl get jobs -l job-name=hello-cronjob
   kubectl describe job hello-cronjob-<timestamp>
   ```
   Check for application errors or resource constraints.

3. **Incorrect scheduling time**
   - Verify the cron expression syntax
   - Check the time zone configuration
   - Ensure the cluster time is correct

4. **Permission issues**
   ```bash
   kubectl auth can-i create cronjob
   ```

### Debugging CronJob Issues

```bash
# Check CronJob status and events
kubectl describe cronjob hello-cronjob

# Check recent Jobs
kubectl get jobs --sort-by=.metadata.creationTimestamp

# Check Job logs
kubectl logs -l job-name=hello-cronjob

# Check CronJob events
kubectl get events --field-selector involvedObject.name=hello-cronjob --sort-by='.lastTimestamp'

# Verify cron expression
kubectl get cronjob hello-cronjob -o jsonpath='{.spec.schedule}'
```

## 📚 Next Steps

After completing this workshop, you can:

1. Create CronJobs with different schedules (hourly, daily, weekly)
2. Use CronJobs for backup and maintenance tasks
3. Implement CronJobs with persistent storage
4. Monitor CronJob performance and resource usage
5. Set up CronJob notifications and alerts
6. Implement CronJob cleanup policies


## 🧹 Cleanup

To clean up the resources created in this workshop:

```bash
kubectl delete -f ./07-schedule-job-with-cronjob/cronjob.yaml
```

**Note**: This will delete the CronJob and all Jobs created by it.

## ⚠️ Important Notes

- CronJobs create Jobs based on the schedule, not at exact times
- Multiple Jobs can run simultaneously if previous Jobs haven't completed
- Consider resource limits and concurrency policies for production CronJobs
- History limits help manage cluster resources by cleaning up old Jobs
- Time zone configuration is important for accurate scheduling
- CronJobs with `"* * * * *"` schedule run every minute (use with caution)

---

**Happy learning! 🚀** 