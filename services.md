Services
=
Deployment makes it easy to create pod replica, scale dynamically, update and replaced.
However, providing network access to those pods for other component is difficult. 
Services provide a layer of abstraction that solves this problem. Client
can simply access service, which dynamically proxy traffic to the current set of 
replica.


```yaml
apiVersion: v1
kind: Deployment
metadata:
    name: nginx-deployment
spec:
    replicas: 3
    selectors:
        matchLabel:
            app: nginx
    template:
        metadata:
            labels:
                name: nginx
        spec:
            container:
                - name: nginx
                  image: nginx:1.7.9
                  ports:
                    - containerPort: 80
```  

service layer

```yaml
apiVersion: v1
kind: Service
metadata:
    name: my-service
spec:
    type: ClusterIP
    selector:
        app: nginx
    ports:
        - protocol: tcp
          port: 8080
          targetPort: 80
```

```shell script
kubectl get svc
kubectl get endpoints my-service
```
