## Overview of Kubernetes

### What is [Kubernetes](https://github.com/kubernetes/kubernetes)?

 It is an open source orchestration system for containers developed by Google and open sourced in 2014. Kubernetes is a useful tool for working with containerized applications. 

Kubernetes was born out of the lessons learned in [scaling containerized apps at Google](https://queue.acm.org/detail.cfm?id=2898444), and is used for automating deployment, scaling and managing containerized applications.

### What are the Benefits of using Kubernetes?

Kubernetes is the standard for container orchestration. 

All major cloud providers support Kubernetes. 

Amazon -  [Amazon EKS](https://aws.amazon.com/eks/)

 Google - [Google Kubernetes Engine GKE](https://cloud.google.com/kubernetes-engine) 

Microsoft - [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/en-us/services/kubernetes-service/).

Kubernetes is a framework for running distributed systems [Planet Scale](https://kubernetes.io/). 

Google uses it to run billions of containers a week.

### Capabilities of Kubernetes 

- High availability architecture
- Auto-scaling
- Rich Ecosystem
- Service discovery
- Container health management
- Secrets and configuration management



### What are the Basics of Kubernetes?

The core operations involved in Kubernetes include creating a Kubernetes Cluster, deploying an application into the cluster, exposing an application ports, scaling an application and updating an application.



### What is the Kubernetes (Cluster) Architecture?

The core of Kubernetes is the cluster. Containers run in the cluster. The core components of the cluster include a cluster master and nodes. Inside nodes there is another hierarchy. This is shown in the diagram. A Kubernetes node can contain multiple pods, which in turn can contain multiple containers and/or volumes.



### Setting Up a Kubernetes Cluster

There are two main methods:

1. Set up a local cluster with Docker Desktop
2. Provision a cloud cluster from cloud providers.

### Launching Containers in a Kubernetes Cluster?

 `docker stack deploy --compose-file` 

#### Sample Compose-File in YAML

```yaml
version: '3.3'

services:
  web:
    image: dockersamples/k8s-wordsmith-web
    ports:
     - "80:80"

  words:
    image: dockersamples/k8s-wordsmith-api
    deploy:
      replicas: 5
      endpoint_mode: dnsrr
      resources:
        limits:
          memory: 50M
        reservations:
          memory: 50M

  db:
    image: dockersamples/k8s-wordsmith-db
```

**Deployment**

```bash
docker stack deploy --namespace my-app --compose-file /path/to/docker-compose.yml mystack
```

### Kubernetes Clusters

**Kubernetes coordinates a highly available cluster of computers that are connected to work as a single unit and automates the distribution and scheduling of application containers across a cluster in a more efficient way.** **

Allows deploy containerized applications to a cluster without tying them specifically to individual machines. 

To make use of this new model of deployment, applications need to be decoupled from their hosts by containerization

A Kubernetes cluster consists of:

- The **Master** to coordinate the cluster
- **Nodes**  that are the workers that run applications

**The Master is responsible for managing the cluster.** 

The master coordinates all activities in the cluster

- Scheduling applications
- Maintaining applications' desired state
- Scaling 
- Rolling Updates

**A Node is a VM/physical computer that serves as a worker machine in a Kubernetes cluster.** 

- Each node has a Kubelet, 
  - An agent for managing the node and communicating with the Kubernetes master. 
- The node also have tools for
  - Handling container operations, such as Docker or rkt. 
- A Kubernetes cluster that handles production traffic should have a **minimum of three nodes.**

***Masters manage the cluster and the nodes that are used to host the running applications.***

When deploying applications on Kubernetes

- You instruct the master to start the application containers. 
  - The master schedules the containers to run on the cluster's nodes. 
  - **The nodes communicate with the master using the [Kubernetes API](https://kubernetes.io/docs/concepts/overview/kubernetes-api/)** that is exposed by the master.
  - Kubernetes API can be used by end users directly to interact with the cluster.

### MiniKube

- Lightweight Kubernetes implementation that creates a VM on your local machine and deploys a simple cluster containing only one node. 
- Minikube CLI provides basic bootstrapping operations for working with your cluster, including start, stop, status, and delete. 

### Summary

- Kubernetes cluster
- Minikube

*Kubernetes is a production-grade, open-source platform that orchestrates the placement (scheduling) and execution of application containers within and across computer clusters.*





