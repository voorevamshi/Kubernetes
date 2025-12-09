

# 🚀 Steps to Create EKS Cluster from Local Machine

1. Have a `clusterconfig` YAML configuration ready.
2. Generate **AWS Access Key** and **Secret Key**.
3. Install **AWS CLI** on your local machine.
4. Configure AWS CLI using:
5. Install **eksctl** tool.
6. Create EKS cluster using:

   ```
   eksctl create cluster -f clusterconfig.yaml
   ```

---

# 🌐 Steps to Create EKS Cluster from Bastion Host

1. Have a `clusterconfig` file ready.
2. Create an IAM Role with **Admin access**.
3. Attach this role to the bastion EC2 instance.
4. Connect to the bastion and copy the config file.
5. Install **eksctl** tool on the bastion.
6. Create EKS cluster:
---



## 1. Install Requirements

### Install AWS CLI

* Download and install from AWS documentation
 https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html
* Verify:

```
aws --version

```

* Configure:

```
aws configure
AWS Access Key ID [****************DF73]: ***
AWS Secret Access Key [****************mifb]: *** 
Default region name [eu-north-1]: us-east-1
Default output format [json]: json

```

### Install kubectl 

Run below commands in Git bash or Power Shall

curl -LO https://dl.k8s.io/release/v1.29.0/bin/windows/amd64/kubectl.exe

move kubectl.exe C:\Windows\System32\

* Verify:
kubectl version --client

```
kubectl version --client
```

### Install eksctl

* Download from eksctl installation guide Using gibash
https://docs.aws.amazon.com/eks/latest/eksctl/installation.html
* Verify:
```
eksctl version
```

## 2. Create `cluster.yaml`

EKS will be created in us-east-1 as your AWS CLI is present in us-east-1

EKS does NOT use your existing EC2 instances. It will create new instances

A new default VPC will be used

Your existing EC2 & VPC will be ignored

Example:

```yaml
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig

metadata:
  name: demo-cluster
  region: us-east-1

nodeGroups:
  - name: ng-1
    instanceType: t3.micro
    desiredCapacity: 2


```

## 3. Create Cluster

```
eksctl create cluster -f cluster.yaml
```

## 4. Verify

```
aws eks list-clusters
kubectl get nodes
```

## 5. Delete Cluster

```
eksctl delete cluster --name demo-cluster --region ap-south-1
```
