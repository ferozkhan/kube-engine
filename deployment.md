Deployment
--------

A deployment is kubernetes object that can be created using a descriptor.

```yaml
apiVersion: app/v1
kind: Deployment
metadata:
    name: nginx-deployment
spec:
    replica: 3
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
                  image: nginx:1.7.9
                  ports:
                    - containerPort: 80 
```

* spec.replica: the number of replica pod
* spec.template: A template pod descriptor which defines the pod which will be created.
* spec.selector: the deployment will manage all pods whose label matches this selector.