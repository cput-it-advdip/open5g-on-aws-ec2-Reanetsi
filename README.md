# Assignment 1: K3s Deployment on AWS
**Name:** REYANESTI RAMAHODI 
**Student Number:** 222313064  
**Course:** Advanced Diploma in IT (Communication Networks)

---

## Explanation of the Architecture
### What is K3s?
K3s is a highly available, certified Kubernetes distribution designed for low-resource environments such as Edge, IoT, and 5G infrastructures. It is packaged as a single binary (<100MB) by removing legacy, alpha, and non-default features found in standard upstream Kubernetes.

### The Key Components
* **Control Plane:** The brain of the cluster, containing the API Server (entry point), Scheduler (assigns pods), and Controller Manager (maintains desired state).
* **Agents (Worker Nodes):** The hosts where the actual containerized workloads run.
* **Container Runtime:** Uses **containerd** as a lightweight, industry-standard runtime.
* **CNI (Flannel):** Manages the L3 networking fabric for Pod-to-Pod communication.
* **Kine:** A shim that allows K3s to use **SQLite** (embedded) instead of the resource-heavy etcd, perfect for edge deployments.
* **ServiceLB & Traefik:** Built-in Load Balancer and Ingress controller for exposing services.

---

## System Requirements that i used :

| Requirement | Control Plane (Server) | Agent (Worker) |
| :--- | :--- | :--- |
| **Instance Type** | t3.medium | t3.small |
| **vCPU** | 2 | 1 |
| **RAM** | 4 GB | 2 GB |
| **Storage** | 20 GB gp3 SSD | 20 GB gp3 SSD |
| **OS** | Ubuntu 22.04 LTS | Ubuntu 22.04 LTS |

---

## 🛠 Installation Steps & Commands

### 1. Provisioning & Security
Configured an AWS VPC with a Security Group allowing:
* `6443/tcp`: Kubernetes API Server
* `8472/udp`: Flannel VXLAN
* `10250/tcp`: Kubelet metrics



### 3. Evidence of Deployment

## 3.1 Cluster Node Status (Master and Worker)
This screenshot confirms that the K3s cluster is operational. It shows both the Control Plane and the Worker node in a **Ready** status, running the latest K3s version.

![Nodes Status](img/nodes_Status.jpeg)

## 3.2 System Pods and Networking Status
The following output shows all system-level pods (CoreDNS, Metrics Server, and Traefik) running across the namespace. This confirms that the internal networking (Flannel) and Ingress (Traefik) are fully functional.

![Pods Status](img/pods_Status.jpeg)

## 3.3 AWS EC2 Management Console
This view from the AWS Console shows the two active EC2 instances (**t3.medium** for the Master and **t3.micro** for the Worker) in the `running` state within the `us-east-1` region.

![AWS Console](img/aws_Console.jpeg)


### 4. Technical Reflection

## 4.1 Development Insights & Technical Competency
This deployment offered a practical deep dive into the lifecycle of cloud-native systems. A key takeaway was the critical dependency between Kubernetes cluster stability and the underlying network architecture. Moving beyond basic script execution, I gained a functional understanding of AWS Security Groups, VPC traffic routing, and the implementation of secure node-to-node authentication via cluster tokens. Differentiating between the Control Plane (Master) and the Agent (Worker) provided a clear perspective on how state and high availability are managed across a distributed virtual environment.

## 4.2 Challenges and Resolutions
The most significant challenge I encountered was a **"Permission Denied (403)"** error when attempting to push my progress to the GitHub repository. Initially, I believed this was a local credential issue, but after troubleshooting, I realized it was due to a lack of write access to the organization’s template. I resolved this by ensuring my local environment was correctly mapped to my personal GitHub Classroom repository.

Technically, I also faced a **Connection Timeout** while joining the worker node to the master. By auditing the AWS Security Groups, I discovered that **Port 6443 (K3s API)** and **UDP Port 8472 (Flannel VXLAN)** were not explicitly permitted. Once these inbound rules were applied to the master’s private IP range, the worker joined successfully and shifted to a "Ready" state. This highlighted the importance of **"Security by Design"** in cloud environments.

## 4.3 K3s, 5G Cloud-Native, and Production Kubernetes
K3s is a cornerstone of **5G Cloud-Native architecture**. In a production 5G ecosystem, low-latency processing must occur at the **"Edge"**—locations near cell towers where hardware resources (CPU/RAM) are limited. Standard Kubernetes (K8s) is often too resource-intensive for these sites. 



K3s provides a lightweight, CNCF-compliant alternative that replaces the heavy etcd database with **SQLite**, allowing us to manage **Containerized Network Functions (CNFs)** with minimal overhead. This ensures that 5G services like the User Plane Function (UPF) can run efficiently on smaller, cost-effective hardware while maintaining the same orchestration power as a full-scale production cluster.

## 4.4 Virtualization and Containerization for Scalability
Virtualization and containerization work in tandem to enable scalable, elastic services. **Virtualization** (via AWS EC2) allows us to abstract physical hardware into multiple isolated virtual machines, providing the flexible foundation needed to deploy a cluster in minutes. **Containerization** (via K3s/containerd) then allows us to package applications and their dependencies into lightweight units that can be deployed across those virtual machines.



This combination enables **horizontal scaling**; if user demand on a 5G network spikes, we can programmatically provision new EC2 instances (Virtualization) and instantly deploy new pods (Containerization) to handle the traffic. This synergy is what allows modern cloud services to remain resilient, portable, and capable of scaling from a single user to millions seamlessly.
