##Create your first Kubernetes Job

Apply the manifest:
```bash
kubectl apply -f hello-job.yaml
```

Check the Job:
```bash
kubectl get jobs
```

Check the Pod:
```bash
kubectl get pods
```
Check the execution logs:
```bash
kubectl logs job/hello-job
```
##Parallel Job: Process 10 tasks

Apply the manifest:
```bash
kubectl apply -f parallel-job.yaml
```
Monitor the Pods:
```bash
kubectl get pods -w
```
Check Job status:
```bash
kubectl get job parallel-task
```
Kubernetes will attempt to achieve 10 successful completions while allowing up to three active Pods at a time.

##CronJob Creation

Let's create a CronJob that generates a report every day at 2 AM Indian Standard Time.

Code contains CronJob manifestation in daily-report.yaml file.

Apply the manifest:
```bash
kubectl apply -f daily-report.yaml
```
Check the CronJob:
```bash
kubectl get cronjob
```
Inspect its configuration:
```bash
kubectl describe cronjob daily-report
```
The next scheduled execution will occur at 2 AM Asia/Kolkata time.

##Suspend and resume a CronJob

There may be situations where you need to temporarily stop scheduled executions.

For example:

* During application maintenance.

* During a database migration.

While investigating an unexpected batch failure.

During a planned infrastructure upgrade.

Suspend future executions:
```bash
kubectl patch cronjob daily-report \
  -p '{"spec":{"suspend":true}}'
```
Resume execution:
```bash
kubectl patch cronjob daily-report \
  -p '{"spec":{"suspend":false}}'
```
Suspending a CronJob does not terminate Jobs that are already running.

Be careful when resuming: missed schedules may be handled according to startingDeadlineSeconds. Without an appropriate deadline, unsuspending can trigger a missed execution.