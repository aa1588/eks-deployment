# Deploy Microservices using Kubernetes - EKS

Micorservices Deployment using Docker and K8s (EKS)

## Table of Contents
- [1. Setup](#eksctl)
  - [Install eksctl on Mac](#install-eksctl-mac)
  - [Install AWS CLI](#install-aws-cli)
  - [AWS Credentials Configuration](#aws-credentials-configuration)
 
- [2. Cluster](#eksctl)
  - [Create Cluster](#create-cluster)
  - [Delete Cluster and All Resources](#delete-cluster-and-all-resources)
 
- [3. Deployment](#eksctl)
  - [Deploy Microservices](#deploy-microservices)
  - [Scale Up currencyservice](#scale-up-currencyservice)
  - [Scale Down currencyservice](#scale-down-currencyservice)
- [Istio (Service Mesh)](#istio-service-mesh)
  - [Download Istio and Configure](#download-istio-and-configure)
  - [Install Istio in the Cluster](#install-istio-in-the-cluster)
  - [Configure Envoy Proxy Injection](#configure-envoy-proxy-injection)
  - [Unlabel Namespace](#unlabel)
  - [Activate Addons for Kiali, Grafana, etc.](#how-to-activate-addons-for-kialigrafana-etc)
  - [Accessing Kiali](#accessing-kiali)
  - [Accessing Prometheus](#accessing-prometheus)
  - [Accessing Grafana](#accessing-grafana)
- [Delete All Pods and Resources](#delete-all-the-pods-and-resources)

---

# 1. Setup
## Install eksctl on Mac
![eksctl Logo](https://repository-images.githubusercontent.com/134539560/11b62283-0ebd-4a07-953d-c373e74bacec)
```bash
$ brew tap weaveworks/tap
$ brew install weaveworks/tap/eksctl
$ eksctl version

```

## Install AWS CLI
![AWS CLI Logo](https://buddy.works/_next/image?url=%2Fblog%2Fthumbnails%2Faws-cli-cover.png&w=1920&q=75)

[AWS CLI Installation Guide](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
```bash
$ which aws
  /usr/local/bin/aws

$ aws --version
  aws-cli/2.18.11 Python/3.12.6 Darwin/23.6.0 exe/x86_64
```


## AWS Credentials Configuration
![AWS Configure](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhG7AO2QCQiJeV51vhRxEgKY0CYtXlVSOswll8I411Uc1WtO_9bov32tKyQxEleEoK2Uuca-9dAZlfkxSLCKe1HEq-uINGcJoAasjARxTLWGnc3CtbPAluA5hOnML3FvjnqYovXLVH4_UqCkNp8bJFYotTVEC-EP8VCO-wOC3e9fwj3cP6QtM1jtiBB/s728-rw-e365/aws.jpg)

Create Access Key from `Security Credentials` option under your AWS Management Console Profile.
```bash
$ aws configure

  AWS Access Key ID [None]: 
  AWS Secret Access Key [None]: 
  Default region name [None]: us-east-1
  Default output format [None]: json
```

# 2. Cluster

## Create Cluster 
```bash
eksctl create cluster \                                                                                             
      --name test-cluster \
      --version 1.30 \
      --region us-east-1 \
      --nodegroup-name linux-nodes \
      --node-type m5.large \
      --nodes 3
```
**kubeconfig (YAML file)**
- After the cluster is created, the metadata about the cluster will be stored at  `$HOME/.kube/config` location which **kubectl client** can use to connect to the cluster later. 

```bash
❯ kubectl get nodes
NAME                             STATUS   ROLES    AGE     VERSION
ip-192-168-16-44.ec2.internal    Ready    <none>   4m43s   v1.30.4-eks-a737599
ip-192-168-52-133.ec2.internal   Ready    <none>   5m6s    v1.30.4-eks-a737599
```

## Delete cluseter and all resources created above

`eksctl delete cluster --name test-cluster`

************************************************************************************************************************************************************

# 3. Deployment

## Deploy Microservices
A **Kubernetes Manifest** file is a YAML or JSON file that describes the desired state of a Kubernetes object. These objects can include deployment, replica set, service, and more. Manifest files define the specifications of the object, such as its metadata, properties, and desired state.



