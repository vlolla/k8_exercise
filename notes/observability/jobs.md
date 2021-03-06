# Jobs

A Job creates one or more Pods and ensures that a specified number of them successfully terminate. As pods successfully complete, the Job tracks the successful completions. When a specified number of successful completions is reached, the task (ie, Job) is complete. Deleting a Job will clean up the Pods it created.

A simple case is to create one Job object in order to reliably run one Pod to completion. The Job object will start a new Pod if the first Pod fails or is deleted (for example due to a node hardware failure or a node reboot).

You can also use a Job to run multiple Pods in parallel.
Running an example Job
Here is an example Job config. It computes π to 2000 places and prints it out. It takes around 10s to complete.
```
apiVersion: batch/v1
kind: Job
metadata:
  name: pi
spec:
  template:
    spec:
      containers:
      - name: pi
        image: perl
        command: ["perl",  "-Mbignum=bpi", "-wle", "print bpi(2000)"]
      restartPolicy: Never
  backoffLimit: 4

  ```

  # Cron Jobs

  A CronJob creates Jobs on a repeating schedule.

  One CronJob object is like one line of a crontab (cron table) file. It runs a job periodically on a given schedule, written in Cron format.

  ### CronJob

CronJobs are useful for creating periodic and recurring tasks, like running backups or sending emails. CronJobs can also schedule individual tasks for a specific time, such as scheduling a Job for when your cluster is likely to be idle.
Example
This example CronJob manifest prints the current time and a hello message every minute:

```
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: throw-dice-cron-job
spec:
  schedule: "30 21 * * *"  # cron job like we do in Linux
  jobTemplate:
    spec:
      completions: 3
      parallelism: 3
      backoffLimit: 25 # This is so the job does not quit before it succeeds.
      template:
        spec:
          containers:
          - name: math-add
            image: kodekloud/throw-dice
          restartPolicy: Never
  ```

## Commands in Job and Cron Job

```
kubectl create –f job-definition.yaml

kubectl get jobs

kubectl explain job

kubectl delete job-name

kubectl create -f cronjob-definition.yaml

kubectl get cronjobs

kubectl delete cronjobs-name

```

## Completions & Parallalism

### Multiple Pods

To run multiple pods with in the job defenition use the completion attribue in job definition file as given below

```
apiVersion: batch/v1
kind: Job
metadata:
  name: throw-dice-job
spec:
  completions: 3
  backoffLimit: 25 # This is so the job does not quit before it succeeds.
  template:
    spec:
      containers:
      - name: math-add
        image: kodekloud/throw-dice
      restartPolicy: Nevermaster $
```

## Parallalism

Instead of getting the pods created sequentially we can get them created in parallel.
For this add a property called parallelism to the job specification. We set it to 3 to
create 3 pods in parallel. So the job first creates 3 pods at once. Two of which
completes successfully. So we only need one more, so it’s intelligent enough to create
one pod at a time until we get a total of 3 completed pods.


```
apiVersion: batch/v1
kind: Job
metadata:
  name: throw-dice-job
spec:
  completions: 3
  parallalism: 3
  backoffLimit: 25 # This is so the job does not quit before it succeeds.
  template:
    spec:
      containers:
      - name: math-add
        image: kodekloud/throw-dice
      restartPolicy: Never
```