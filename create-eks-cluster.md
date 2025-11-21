# Create EKS Cluster from EC2 Machine

Steps To Create EKS Cluster From Bastion Instance
1) You need to have clusterconfig file.
2) Create IAM Role with admin access.
3) Attach this role to bastion instance.
4) Connect to the machine and copy clusterconfig file from local machine.
5) Install EKSCTL tool.
6) Create EKS cluster.

# Create EKS Cluster from Local Machine (Windows)


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
Default region name [eu-north-1]: eu-north-1
Default output format [json]: json

```

### Install kubectl 

* Download 
* Verify:

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

Example:

```yaml
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig
metadata:
  name: demo-cluster
  region: ap-south-1

nodeGroups:
  - name: ng-1
    instanceType: t3.medium
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
