
get pod, services, and network policies.

```shell script
kubecctl get pod
kubectl get service
kubectl get networkpolicy
kubectl edit pod web-gateway

kubectl describe networkpolicy inventory-policy

```


check access:

```shell script
kubectl exec web-gateway -- curl -m 3 inventory-svc
```