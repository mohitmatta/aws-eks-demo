# aws-eks-demo

This repo will serve as a guide and provide step by step instruction to deploy sample flask API python K8 application using eksctl. 

## Step - 1 : Create EKS Management Host in AWS ##

1) Launch new Ubuntu VM using AWS Ec2 ( t3.micro )	  
2) Connect to machine and install kubectl using below commands

```
curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin
kubectl version --short --client
```

3) Install AWS CLI latest version using below commands:
   
```
sudo apt install unzip
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
aws --version
```

4) Install eksctl using below commands:
   
```
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
eksctl version
```

5) Install aws-iam-authenticator using below commands:

```
curl -o aws-iam-authenticator https://amazon-eks.s3.us-west-2.amazonaws.com/1.15.10/2020-02-22/bin/linux/amd64/aws-iam-authenticator
chmod +x ./aws-iam-authenticator
sudo mv ./aws-iam-authenticator /usr/local/bin
```


## Step - 2 : Create IAM role & attach to EKS Management Host ##

1) Create New Role using IAM service ( Select Usecase - ec2 ) 	
2) Add below permissions for the role
	- Administrator - access 
3) Enter Role Name
4) Attach created role to EKS Management Host (Select EC2 --> Click on Security --> Modify IAM Role --> attach IAM role we have created)

## Step - 3 : Create EKS Cluster using eksctl ## 

## a) Declarative way:

While you can use the AWS Console, the standard way is to use Infrastructure as Code (IaC). We'll use a declarative cluster.yaml to ensure our infra is reproducible.

```
eksctl create cluster -f cluster.yaml
```

```
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig

metadata:
   name: eksdemo
   region: us-east-1

nodeGroups:
   - name: ng-1
      instanceType: t3.micro
      desiredCapacity: 2
      minSize: 1
      maxSize: 3
      ssh:
      allow: false
```

## b) Managed Node Groups:

```
# cluster.yaml
# A cluster with a managed nodegroup with "availabilityZones"
---
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig

metadata:
  name: eksdemo
  region: us-east-1
  #version: "1.23"

availabilityZones: ["us-east-1a", "us-east-1c"]
managedNodeGroups:
  - name: managed-ng
    instanceType: t3.micro
    minSize: 2
    maxSize: 3
    availabilityZones: ["us-east-1a"]
```

```
eksctl create nodegroup --config-file=eks-managed-ng.yaml
```

## c) eksctl arguments:

We can also create it using the below eksctl command:

```
eksctl create cluster --name eksdemo --nodegroup-name ng-grp-default  --node-type t3.small --nodes 2 --region us-east-1 --zones us-east-1a,us-east-1b
```

## Note: Cluster creation will take 10 to 15 minutes of time. After cluster is  created successfully we can check nodes using below command.

```
 kubectl get nodes  
```

## Step - 4 : Deploy application using deployment and expose it to public using load balancer ## 

```
kubectl apply -f deployment.yaml
```

```
kubectl apply -f service.yaml
```

All deployment configurations are available in this github repository.
