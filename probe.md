
Liveness Probe
-

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: liveness-pod
spec:
  containers:
    - name: app-container
      image: busybox
      command: ['sh', '-c', 'echo hello, kube && sleep 3600']
      livenessProbe:
        exec:
          command:
            - echo
            - testing
        initialDelaySeconds: 5
        periodSeconds: 5
```

Readiness Probe
-

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: rediness-pod
spec:
  containers:
    - name: app-container
      image: nginx
      readinessProbe:
        httpGet:
          path: /
          port: 80
        initialDelaySeconds: 5
        periodSeconds: 5
```