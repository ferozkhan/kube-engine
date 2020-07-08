
KUBERNETES DEV
==================



Setup kubernetes cluster 
-----------

```bash
#!/bin/bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

sudo add-apt-repository    "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"

curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -

cat << EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF

sudo apt-get update

sudo apt-get install -y --allow-change-held-packages docker-ce=18.06.1~ce~3-0~ubuntu kubelet=1.18.5-00 kubeadm=1.18.5-00 kubectl=1.18.5-00
sudo apt-mark hold docker-ce kubelet kubeadm kubectl # to prevent automatic update


echo "net.bridge.bridge-nf-call-iptables=1" | sudo tee -a /etc/sysctl.conf # properly network to function
sudo sysctl -p
```

# to be run in master node

```shell script
### to initialize the cluster with default mask

sudo kubeadm init --pod-network-cidr=10.244.0.0/16


mkdir -p $HOME/.kube

sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config

sudo chown $(id -u):$(id -g) $HOME/.kube/config

# without Flannel networking, nodes won't start and throw networking error.
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/3f7d3e6c24f641e7ff557ebcea1136fdf4b1b6a1/Documentation/kube-flannel.yml
```


Once master node is ready, connect worker nodes using join command:

sudo kubeadm join $controller_private_ip:6443 --token $token --discovery-token-ca-cert-hash $hash
####
kubeadm join 172.31.118.109:6443 --token 400zp2.d7p3n38xtgx11owi --discovery-token-ca-cert-hash sha256:0f3b048a64ec5d049e24059e6c7962ee3edebbe87068c118c26f9d0708f28637



# command reference
```shell script
kubectl api-resources -o name
kubectl get pods -n kube-system
kubectl get nodes
kubectl get nodes $node_name
kubectl get nodes $node_name -o yaml
kubectl describe node $node_name
```

PODS
----------------

Pods are basic building blocks and way to go about containers in kubernetes. 
pods can have one or more containers shares same set of resources.
```shell script
kubectl create -f my-pod.yml
kubectl apply -f mypod.yml # edit and apple configuration
kubectl edit pod my-pod
kubectl delete pod my-pod

```


pod.yml
```yaml
apiVersion: v1
    kind: Pod
    metadata:
        name: app
    labels:
    	app: myapp
    containers:
        - name: app-container-name
          image: image-name
          command: ####
```

### NAMESPACES

this provide a way to keep your objects organized within a cluster. When no namespace is specified, the cluster will assume default namespace.

pod.yml
```yaml
apiVersion: v1
	kind: Pod
	metadata:
		name: app
		namespace: my-ns
	labels:
		app: my-app
	containers:
		- name: app-container-name
		  image: image-name
		  command: ####
```

: use -n flag to pass namespace value.
```shell script
kubectl get pods -n my-ns
kubectl describe pod my-pod -n my-ns

```

Basic container configuration
--------------------------------
Custom command for a container can be defined. e.g.
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-command-pod
  labels:
    app: myapp
spec:
  containers:
  - name: myapp-container
    image: busybox
    command: ['echo']
  restartPolicy: Never
```

This will run echo command and shutdown. And because of `restartPolicy: Never`, container
will not start again.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-args-pod
  labels:
    app: myapp
spec:
  containers:
  - name: myapp-container
    image: busybox
    command: ['echo']
    args: ['This is my custom argument']
  restartPolicy: Never
```

additional arguments are passed using args.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-containerport-pod
  labels:
    app: myapp
spec:
  containers:
  - name: myapp-container
    image: nginx
    ports:
    - containerPort: 80
```

Example of POD running nginx container.


Config Maps
-------------------
Its a kubernetes object that represents a key-value store. This offer a centralized
place to put the configuration need to pass into Pods or Containers. 

Then changing the configuration is the matter of updating configMap and restarting Pods.

`config_map.yml`
```yaml
apiVersion: v1
kind: ConfigMap
meta:
    name: my-config-map
data:
    myKey: myValue
```

```yaml
apiVersion: v1
kind: Pod
metadata:
    name: pod-with-config
    labels:
        app: config-app
spec:
    containers:
    - name: config-with-container
      image: busybox
      command: ['sh', '-c', 'echo $(MY_VAR) && sleep 3600']
      env:
      - name: MY_VAR
        valueFrom:
          configKeyMapRef:
            name: my-config-map
            key: myKey
```

configuration as mounted volume.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-configmap-volume-pod
spec:
  containers:
  - name: myapp-container
    image: busybox
    command: ['sh', '-c', "echo $(cat /etc/config/myKey) && sleep 3600"]
    volumeMounts:
      - name: config-volume
        mountPath: /etc/config
  volumes:
    - name: config-volume
      configMap:
        name: my-config-map
```

commands to explore how configmap interacts with pods and container:

```yaml
kubectl logs my-configmap-pod

kubectl logs my-configmap-volume-pod

kubectl exec my-configmap-volume-pod -- ls /etc/config

kubectl exec my-configmap-volume-pod -- cat /etc/config/myKey
```

Security Context
---------------

To interacts with underlying security mechanism of the host operating system.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-securitycontext-pod
spec:
  containers:
  - name: myapp-container
    image: busybox
    command: ['sh', '-c', "cat /message/message.txt && sleep 3600"]
    volumeMounts:
    - name: message-volume
      mountPath: /message
  volumes:
  - name: message-volume
    hostPath:
      path: /etc/message
```

```shell script
kubectl delete pod my-securitycontext-pod --now
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-securitycontext-pod
spec:
  securityContext:
    runAsUser: 2000
    fsGroup: 3000
  containers:
  - name: myapp-container
    image: busybox
    command: ['sh', '-c', "cat /message/message.txt && sleep 3600"]
    volumeMounts:
    - name: message-volume
      mountPath: /message
  volumes:
  - name: message-volume
    hostPath:
      path: /etc/message
```

Resource Requirements
----------

Way to tell kubernetes to run a container with requested resources in terms of memory and cpu.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-resource-pod
spec:
  containers:
  - name: myapp-container
    image: busybox
    command: ['sh', '-c', 'echo Hello Kubernetes! && sleep 3600']
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
```

Secrets
-----

To pass sensitive information into the pods:

secret.yaml

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-secret
stringData:
  myKey: myPassword
```

delete once the secret is created.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-secret-pod
spec:
  containers:
  - name: myapp-container
    image: busybox
    command: ['sh', '-c', "echo Hello, Kubernetes! && sleep 3600"]
    env:
    - name: MY_PASSWORD
      valueFrom:
        secretKeyRef:
          name: my-secret
          key: myKey
```

Service Accounts
----

To interacts with kubernets api from within container:

```shell script
kubectl create serviceaccount my-serviceaccount
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-serviceaccount-pod
spec:
  serviceAccountName: my-serviceaccount
  containers:
  - name: myapp-container
    image: busybox
    command: ['sh', '-c', "echo Hello, Kubernetes! && sleep 3600"]
```
