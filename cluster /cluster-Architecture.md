# Kubernetes – Quick Notes & EKS Setup Guide

## 📌 Cluster Architecture

![21_ClusterArchitecture](https://github.com/user-attachments/assets/107aa34a-89a9-4289-b700-ef7af46cfe2e)

A Kubernetes cluster consists of:

* **Master / Control Plane**
* **Worker Nodes / Data Plane**

---

## 🧠 Master (Control Plane) Components

### **kube-apiserver**

* Receives all commands and requests.
* Performs authentication and authorization.
* The only component that interacts with all other components in the cluster.

### **etcd**

* A key-value database that stores complete Kubernetes cluster state.
* Any component requesting cluster data gets it from `etcd`.

### **scheduler**

* Decides on which node a pod should run.
* Factors considered:

  * Available CPU & Memory
  * Scheduling rules:

    * NodeName
    * Labels & Selectors
    * NodeAffinity
    * PodAffinity
    * PodAntiAffinity
    * Taints & Tolerations

### **controller-manager**

Controls and manages the entire cluster. It includes multiple controllers:

#### **Replicaset Controller Manager (rs cm)**

* Recreates pods if deleted (ensures desired number of replicas).

#### **Namespace Controller Manager (ns cm)**

* Creates default resources like service accounts when a namespace is created.

#### **Deployment Controller Manager**

* Creates ReplicaSets whenever we create a Deployment.

#### **Node Controller Manager**

* Monitors node status and takes action if a node is unreachable.

#### **Cloud Controller Manager**

* Present only in cloud-managed clusters.
* Helps Kubernetes integrate with cloud services.

---

## 🖥 Worker (Data Plane) Components

## kubelet

* Runs on every worker node.
* Ensures that the node’s running state matches the desired configuration.
* Reports node and pod status to kube-apiserver.
* Interacts with container runtime to start/stop containers.

## kube-proxy

* Runs on every node.
* Manages networking rules using iptables/IPVS.
* Enables pod-to-pod and service-to-pod communication across nodes.

---

## 🧰 Common Component

### **Container Runtime Engine**

* Responsible for running containers inside pods.
* Examples: Docker, containerd, CRI-O.

---

## Container Runtime Engine (CRE)

**CRE (Container Runtime Engine)** is the actual runtime that executes containers inside pods.

### Responsibilities:

* Pulls images.
* Starts and stops containers.
* Manages container process lifecycle.

### Examples:

* Docker
* containerd
* CRI-O
* rkt (deprecated)

---

## Container Runtime Interface (CRI)

**CRI (Container Runtime Interface)** is a standard Kubernetes API that allows kubelet to communicate with any container runtime in a consistent way.

### Why CRI exists:

* Kubernetes needed a common interface to support multiple runtimes.
* Avoids hardcoding support for each runtime in Kubernetes.

### Architecture Flow:

```
kubelet → CRI → container runtime (containerd, CRI-O, etc.)
```

---

# 🔁 Putting It All Together – Full Flow

### 1. User Deploys a Pod

```
kubectl apply -f pod.yaml
```

### 2. kube-apiserver Validates and Stores State

* kube-apiserver processes the request.
* Stores the desired state in etcd.

### 3. kubelet Acts on the Node

* kubelet watches kube-apiserver.
* Sees that a new pod must be created.
* Tells the container runtime engine (Docker/containerd/CRI-O) to start the container.
* Sends status back to kube-apiserver.

### 4. kube-proxy Enables Networking

* If the pod is part of a Kubernetes Service:

  * kube-proxy updates routing rules (iptables/IPVS).
  * Enables traffic to reach the pod.

### Relationships Summary:

| Component      | Communicates With         |
| -------------- | ------------------------- |
| kube-apiserver | etcd, kubelet, kube-proxy |
| etcd           | kube-apiserver only       |
| kubelet        | kube-apiserver            |
| kube-proxy     | kube-apiserver            |

kubelet and kube-proxy do **not** directly talk to etcd.

---


Let me know if you want formatting changes, diagrams, or more sections!
