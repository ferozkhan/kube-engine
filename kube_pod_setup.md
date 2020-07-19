setup
--------------

1: create config map

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: candy-service-config
data:
  candy.cfg: |-
    candy.peppermint.power=100000000
    candy.nougat-armor.strength=10
```
    
```shell script
kubectl apply -f candy-service-config.yml
```

2: create secret

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-password
stringData:
  password: Kub3rn3t3sRul3s!
```

```shell script
kubectl apply -f db-password-secret.yml
```

3: create pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: candy-service
spec:
  securityContext:
    fsGroup: 2000
  serviceAccountName: candy-svc
  containers:
  - name: candy-service
    image: linuxacademycontent/candy-service:1
    volumeMounts:
      - name: config-volume
        mountPath: /etc/candy-service
    env:
    - name: DB_PASSWORD
      valueFrom:
        secretKeyRef:
          name: db-password
          key: password
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
  volumes:
  - name: config-volume
    configMap:
      name: candy-service-config
```

```shell script
kubectl apply -f candy-service-pod.yml
```