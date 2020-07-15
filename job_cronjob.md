
Job
=

Jobs can be used to reliably execute a workload until it completes. 
The job will create one or more pods. When the job is finished, the container
will exit and pod will enter into `Completed` state.

```yaml
apiVersion: batch/v1
kind: Job
metatada:
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

```shell script
kubectl get job
```

Cronjob
=

```yaml
apiVersion: batch/v1beta1
kind: CronJob
metadata:
    name: hello
spec:
    schedule: "*/1 * * * *"
    jobTemplate:
        spec:
            template:
                spec:
                    containers:
                        - name: hello
                            image: busybox
                            args:
                            - /bin/sh
                            - -c
                            - date; echo Hello from the Kubernetes cluster
                    restartPolicy: OnFailure
```

```shell script
kubectl get cronjobs
```