
Deployment
=


```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
    name: rolling-update
spec:
    replicas: 3
    selector:
        matchLabels:
            label: nginx
    template:
        metadata:
            labels: 
                app: nginx
        spec:
            containers:
                - name: nginx
                  image: nginx:1.7.1
                  ports:
                    - containerPort: 80
```

### Perform a rolling update:

```shell script
kubectl set image deployment/rolling-deployment nginx: nginx:1.7.1 --record
```

### Explore deployment history


```shell script
kubectl rollout history deployment/rolling-deployment
kubectl rollout history deployment/rolling-deployment --to-revision=1
```

### Undo a broken deployment

```shell script
kubectl rollout undo deployment/rolling-deployment
kubectl rollout undo deployment/rolling-deployment --to-revision=1 
```


### maxSurge and maxUnavailable

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
    name: rolling-deployment
spec:
    strategy:
        rollingUpdate:
            maxSurge: 25%
            maxUnavailable: 25%
    replicas: 3
    selector:
        matchLabels:
            app: nginx
    template:
        metadata:
            labels:
                app: nginx
        spec:
            containers:
                - name: nginx
                  image: nginx:1.7.1
                  ports:
                    - containerPort: 80
```

