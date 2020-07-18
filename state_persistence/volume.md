
Container storage is designed to be as temporary as the containers themselves. However, sometimes we need storage that is able to survive beyond the short life of a container. Kubernetes volumes allow us to mount storage to a container that isn't in the container itself. In this lesson, we will discuss volumes and demonstrate how to mount a simple volume to a container in a pod.

```yaml
apiVersion: v1
kind: Pod
metadata:
    name: volume-pod
spec:
    containers:
        - image: busybox
          name: busybox
          command: ["sh", "-c", "while true; do sleep 3600; done"]
          volumeMounts:
            - mountPath: /tmp/storage
              name: my-volume
    volumes:
        - name: my-volume
          emptyDir: {}
```