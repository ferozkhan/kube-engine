Our company has a Kubernetes cluster that is running several pods and services. A service called oauth-provider in the gem namespace has stopped working. Our task is to investigate the service and determine what is causing the problem, save some relevant data for future analysis, and then fix the problem.

The broken service is oauth-provider. It is located in the gem namespace.
Identify which Kubernetes object is broken and causing the service to fail. Save the name of the object in the file /usr/ckad/broken-object-name.txt.
Save the object's summary data in JSON format to the file /usr/ckad/broken-object.json.
Edit the object to fix the problem.
The oauth-provider service is a NodePort service listening on port 30080, so we can test it using curl localhost:30080.
Note that there may be other objects in the cluster which appear to be broken. Our task is to identify what specifically is causing the oauth-provider service to fail. Ignore any object which doesn't affect the oauth-provider service.


```shell script
kubectl describe service oauth-provider -n gem
```

Note that the service's selector selects pods with the label `role: oauth`.

```shell script
kubectl get pods --all-namespaces --show-labels
```

Save the broken pod summary in json format:

```shell script
kubectl get pod bowline -n gem -o json > /usr/ckad/broken-object.json
```

FIX:

```shell script
kubectl describe pod bowline -n gem
kubectl edit pod bowline -n gem
kubectl get pod bowline -n gem
```

Test:

```shell script
curl localhost:30080
```