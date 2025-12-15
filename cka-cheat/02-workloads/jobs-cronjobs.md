### Job completion
A Job is considered finished when:
- its status condition is `Complete`
- the required number of successful Pods is reached

Pods are disposable; the Job object is the source of truth.



### Inspect

kubectl get jobs
kubectl describe job <name>
kubectl get cronjobs
kubectl describe cronjob <name>
kubectl get pods

### Create (fast)

kubectl create job <name> --image=<image> -- <command>
kubectl create cronjob <name> --image=<image> --schedule="<cron>" -- <command>

### Debug

kubectl logs <pod>
kubectl describe pod <pod>

### Cleanup

kubectl delete job <name>
kubectl delete cronjob <name>



Case 1: Job never completes

Symptoms:

    Pods keep restarting

    Job stuck in Active

Causes:

    Command exits with non-zero code

    backoffLimit not reached yet

Fix:

    Inspect pod logs

    Fix command or image



Case 2: Job fails too quickly

Symptoms:

    Job status = Failed

Cause:

    backoffLimit reached

Fix:

    Increase backoffLimit

    Fix container command




Case 3: CronJob not running

Symptoms:

    CronJob exists

    No Jobs created

Causes:

    Wrong cron schedule

    startingDeadlineSeconds missed

    Suspended CronJob

Fix:

    Validate cron syntax

    Check suspension flag




Case 4: Jobs piling up

Symptoms:

    Many old Jobs

Cause:

    No history limits set

Fix:

Set successfulJobsHistoryLimit / failedJobsHistoryLimit