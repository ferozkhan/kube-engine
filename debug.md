
```shell script
kubectl get pod
kubectl get pod -n <namespace>

kubectl describe pod <pod-name> -n <namespace>

kubectl edit pod <pod-name> -n <namespace>
```

If pod need to be deleted in order to rectify the problem. Copy the specification before deleting.

```shell script
kubectl get pod <pod-name> -n <namespace> -o yaml --export > spec.yaml

kubectl delete pod <pod-name> -n <namespace>
kubectl apply -f spec.yaml -n <namespace>
```