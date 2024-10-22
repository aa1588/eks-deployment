# eks-deployment
Micorservices Deployement using Docker and K8s (EKS)



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
After the cluster is created, the details about the cluster will be stored at `2024-10-22 15:14:55 [✔]  saved kubeconfig as "/Users/bluestone/.kube/config"` location which `eksctl` can use to connect to the cluster later.
