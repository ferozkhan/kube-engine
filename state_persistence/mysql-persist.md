
A pod running mysql container and persisting data.

create a persistent volume:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
        name: mysql-pv
spec:
        storageClassName: localdisk
        capacity:
                storage: 1Gi
        accessModes:
                - ReadWriteOnce
        hostPath:
                path: "/mnt/data"
```

```shell script
kubectl apply -f mysql-pv.yaml
```

create a persistent volume claim:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
        name: mysql-pvc
spec:
        storageClassName: localdisk
        accessModes:
                - ReadWriteOnce
        resources:
                requests:
                        storage: 500Mi
```

```shell script
kubectl apply -f mysql-pvc.yaml
```

create a mysql pod

```yaml
apiVersion: v1
kind: Pod
metadata:
        name: mysql-pod
spec:
        containers:
                - image: mysql:5.7
                  name: mysql
                  ports:
                          - containerPort: 3306
                  env:
                          - name: MYSQL_ROOT_PASSWORD
                            value: password
                  volumeMounts:
                          - name: mysql-storage
                            mountPath: /var/lib/mysql
        volumes:
                - name: mysql-storage
                  persistentVolumeClaim:
                          claimName: mysql-pvc
```

```shell script
kubectl apply -f mysql-pod.yaml
```

```shell script
kubectl get pv
kubectl get pvc
kubectl get pod
kubectl get nodes # to see worker node
```


