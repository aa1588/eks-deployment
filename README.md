# eks-deployment
Micorservices Deployement using Docker and K8s (EKS)

### Install eksctl Mac
```bash
$ brew tap weaveworks/tap
$ brew install weaveworks/tap/eksctl
$ eksctl version

```

### Install AWS CLI
Link - https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html

```bash
$ which aws

/usr/local/bin/aws

$ aws --version

aws-cli/2.18.11 Python/3.12.6 Darwin/23.6.0 exe/x86_64
```

### AWS Credentials Configuration
Create Access Key from `Security Credentials` option under your AWS Management Console Profile.
```bash
  $ aws configure

AWS Access Key ID [None]: 
AWS Secret Access Key [None]: 
Default region name [None]: us-east-1
Default output format [None]: json
```


### Create cluster 
```bash
eksctl create cluster \
--name test-cluster \
--version 1.30 \
--region us-east-1 \
--nodegroup-name linux-nodes \
--node-type t2.micro \
--nodes 2
```
After the cluster is created, the details about the cluster will be stored at `2024-10-22 15:14:55 [✔]  saved kubeconfig as "/Users/bluestone/.kube/config"` location which `kubectl` can use to connect to the cluster later.
Now, kubectl command should work with "/Users/bluestone/.kube/config".

```bash
❯ kubectl get nodes
NAME                             STATUS   ROLES    AGE     VERSION
ip-192-168-16-44.ec2.internal    Ready    <none>   4m43s   v1.30.4-eks-a737599
ip-192-168-52-133.ec2.internal   Ready    <none>   5m6s    v1.30.4-eks-a737599
```

### Delete cluseter and all resources created above

`eksctl delete cluster --name test-cluster`




# Istio (Service Mesh)

### Download istio and configure
1. Download Istio Package on your local machine - `https://github.com/istio/istio/releases/tag/1.23.2`
2. Unzip the package and move to working directory.
3. open the package, `bin` folder contains `istioctl` and `samples > addons` contains kiali, grafana and prometheus manifests.

4. $ istioctl
zsh: command not found: istioctl

Now, we need to set the PATH.

5. export PATH=$PATH:/Users/bluestone/Desktop/eks-istio-demo/istio-1.23.2/bin    (Only works on the terminal instance where this command is run)
6. $ istioctl
   This should print details about istioctl.

### Install istio in the cluster
$ istioctl install 

Press 'Y' when prompted.

$ kubectl get ns

NAME              STATUS   AGE
default           Active   143m
**istio-system      Active   57s**
kube-node-lease   Active   143m
kube-public       Active   143m
kube-system       Active   143m


### Configure Envoy Proxy Injection
Since we have deployed our microservices in default namespace, we want to add istio label in the default namespace.


$ `kubectl get ns default --show-labels`  // show lablels for default namespace
```
NAME      STATUS   AGE    LABELS
default   Active   126m   kubernetes.io/metadata.name=default
```

$ `kubectl label namespace default istio-injection=enabled`

$ `kubectl get ns default --show-labels`
```
NAME      STATUS   AGE    LABELS
default   Active   130m   stio-injection=enabled,kubernetes.io/metadata.name=default
```

Now, shut down all the pods running and recreate them to see all the proxies injected to each pods.

`$ kubectl delete -f kubernetes-manifests.yaml`
`$ kubectl apply -f kubernetes-manifests.yaml`

### How to activate addons for Kiali,Grafana etc...
```
$ kubectl get pods -n istio-system

NAME                                    READY   STATUS    RESTARTS   AGE
istio-ingressgateway-64f9774bdc-65shr   1/1     Running   0          10m
istiod-868cc8b7d7-67k7n                 1/1     Running   0          10m
```

$ kubectl apply -f istio-1.23.2/samples/addons

```
`$ kubectl get pods -n istio-system`

NAME                                    READY   STATUS    RESTARTS   AGE
grafana-7f76bc9cdb-h7blc                1/1     Running   0          39s
istio-ingressgateway-64f9774bdc-65shr   1/1     Running   0          13m
istiod-868cc8b7d7-67k7n                 1/1     Running   0          13m
jaeger-66f9675c7b-hvmmr                 1/1     Running   0          38s
kiali-65c46f9d98-wjzfd                  1/1     Running   0          37s
loki-0                                  0/1     Pending   0          36s
prometheus-7979bfd58c-j4dpl             2/2     Running   0          35s

```

### Accessing Kiali

`$ kubectl get svc -n istio-system`

```
❯ kubectl port-forward svc/kiali -n istio-system 20001
Forwarding from 127.0.0.1:20001 -> 20001
Forwarding from [::1]:20001 -> 20001
```

### Accessing Prometheus

 `kubectl port-forward svc/prometheus -n istio-system 9090`

 ### Accessing Grafana
 
 `kubectl port-forward svc/grafana -n istio-system 3000`
