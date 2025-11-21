# Kubernetes – Quick Notes & EKS Setup Guide

## 📌 Cluster Architecture

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

### **kubelet**

* Runs on every worker node.
* Reports node status and activity to kube-apiserver.
* Executes instructions from the master.

### **kube-proxy**

* Enables networking across nodes.
* Allows pod-to-pod communication.

---

## 🧰 Common Component

### **Container Runtime Engine**

* Responsible for running containers inside pods.
* Examples: Docker, containerd, CRI-O.

---

## 🔤 Important Full Forms

| Short | Meaning                       |
| ----- | ----------------------------- |
| M     | Master                        |
| W     | Worker                        |
| rs cm | Replicaset Controller Manager |
| ns cm | Namespace Controller Manager  |



---

Let me know if you want formatting changes, diagrams, or more sections!
