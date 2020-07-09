Multi-container pods
----------------

Multi-container pods are simply pods with more than one container that all work together as single unit.

```yaml
apiVersion: v1
kind: Pod
metadata:
    - name: multi-container-pod
spec:
    containers:
    - name: nginx
      image: nginx:1.15.8
      ports:
      - containerPort: 80
    - name: db
      image: mysql:5.7
      ports:
      - containerPorts: 3306
```

In multi-container pods, container interacts with each other via following mechanism:

1. Shared network
    * communicate on ports
2. Shared Storage Volumes
    * share data via mount volume
3. Shared Processes Namespace 
    * shareProcessNamespace: true, to enable process communication