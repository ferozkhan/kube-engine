
Labels, Selctors and Annotations
=

Labels, selectors and annotations are key-value pair that we can attached to kuberenetes object.

Labels
=

: with production label

```yaml
apiVersion: v1
kind: Pod
metadata:
    name: my-app
    labels:
        app: my-app
        environment: production
spec:
    containers:
        - name: nginx
          image: nginx
```

: with development label

```yaml
apiVersion: v1
kind: Pod
metadata:
    name: my-app
    labels:
        app: my-app
        environment: development
spec:
    containers:
        - name: nginx
          image: nginx
```

Selectors
-----------

search pods using selectors with `-l` flag.

```shell script
kubectl get pods -l app=myapp
kubectl get pods -l environment=myappproduction
kubectl get pods -l 'environment in (development,production)'
```

Annotation
=

```yaml
apiVersion: v1
kind: Pod
metadata:
    name: my-app
    annotation:
        name: ferozkhan27@gmail.com
        git-commit: bg334s
spec:
    containers:
        - name: nginx
          image: nginx
```