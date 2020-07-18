PersistanceVolumes and PersistanceVolumeClaims
-

PV and PVC provide a way to easily easily consume storage resources. Specially, in the context of
complex porduction environmet that uses multiple storage solutions.


PVC is an abstraction layer between pod and actual storage.

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
    - name: my-pv
spec:
    storageClassName: local-storage
    capacity:
        storage: 1Gi
    accessModes:
        - ReadWriteOnce
    hostPath:
        path: "/mnt/data"
```

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
    name: my-pvc
spec:
    storageClassName: local-storage
    accessModes:
        - ReadWriteOnce
    resources:
        requests:
            storage: 512Mi
```


```shell script
kubectl get pv
kubectl get pvc
```


Pod consumes storage resources using PVC.

```yaml
apiVersion: v1
kind: Pod
metadata:
    - name: my-pvc-pod
spec:
    containers:
        - image: busybox
          name: busybox
          volumeMounts:
            - mountPath: "/mnt/storage"
              name: my-storage
    volumes:
        - name: my-storage
          persistentVolumeClaim:
            claimName: my-pvc
```