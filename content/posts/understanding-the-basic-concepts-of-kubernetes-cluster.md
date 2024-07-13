---
title: "Understanding the Basic Concepts of Kubernetes(k8s) Cluster"
date: 2024-07-09T12:15:39+10:00
draft: false
showToc: true
TocOpen: true
tags : [
  "k8s",
  "Kubernetes",
  "DevOps",
  ]
categories : [
  "DevOps",
  "k8s",
  "Kubernetes"
]
keywords: [
  "Understanding the Basic Concepts of Kubernetes(k8s) Cluster",
  "Basic Concepts of Kubernetes Cluster",
  "Kubernetes for architecture",
  "Components of Master node in Kubernetes",
  "Components of Worker node in Kubernetes",
  "Kubernetes Cluster Architecture", 
  "Kubernetes Master and Worker nodes",
  "Understanding master and worker nodes in Kubernetes",
  "Kubernetes basic concepts", 
  "Understanding Kubernetes", 
  "Kubernetes for beginners",
  "beginners guide for Kubernetes(k8s) Cluster",
  "Kubernetes cluster components", 
  "How Kubernetes works?", 
  "Introduction to Kubernetes"
]
cover:
  image: "images/posts/understanding-the-basic-concepts-of-kubernetes-cluster/understanding-the-basic-concepts-of-kubernetes-cluster.png" # image path/url
  alt: "Understanding the Basic Concepts of Kubernetes(k8s) Cluster"
  caption: "Kubernetes Cluster Architecture"
  relative: false
  author: "Prakash Bhandari"
description: "As a beginner, understanding a Kubernetes (k8s) cluster might be a little bit complicated. 
In this blog post, I will try to simplify the basic concepts  and components of a Kubernetes (k8s) cluster."
---

As a beginner, understanding a Kubernetes (k8s) cluster might be a little bit complicated. If you go through the k8s official documentation its overwhelming.

In my personal view, the best way to learn any new tool and technology is by understanding its core concepts and architecture.

In this blog post, I will try to simplify the basic concepts and common components of a Kubernetes (k8s) cluster with simple architecture diagram. 

# What is a Kubernetes Cluster?
The simplest definition of a Kubernetes(k8s) cluster is a set of nodes that run containerized applications. Kubernetes cluster has two types on nodes
1. Master Node
2. Worker Node
   
A cluster can be simple, with one master node and one worker node, or it can be complex, with many master nodes and many worker nodes.

I have presented a Kubernetes cluster architecture diagram, this diagram presented a Kubernetes cluster architecture with one master nodes
and two worker nodes. Each node is running two pods. One pods is running one container
inside, while another one is running more two containers inside the pod.

![Kubernetes Cluster Architecture](/images/posts/understanding-the-basic-concepts-of-kubernetes-cluster/understanding-the-basic-concepts-of-kubernetes-cluster.png#center)


## Master Node
The master node is the control plane of Kubernetes which is responsible for controlling and managing whole cluster. 
It maintains the overall state of the cluster, coordinating tasks such as scheduling applications,and managing cluster-wide resources.

For example, if one worker node fails, the master node moves the workload to another healthy worker node.

And also master node exposes the API to the developer or operator via cli tool (kubectl) or admin UI.

At least one master node is required in a Kubernetes (k8s) cluster, but in practice,
there can be more than one master node for high availability and fault tolerance. 
In a nutshell, high availability and fault tolerance mean having a system or network with minimal downtime.

It is always recommended to add redundancy to the master node. 
If one master node goes down, another will automatically take over.

The architecture of the high availability and fault tolerance kubernetes cluster can be like below diagram.

![The architecture of the high availability and fault tolerance Kubernetes cluster](/images/posts/understanding-the-basic-concepts-of-kubernetes-cluster/architecture-of-the-high-availability-and-fault-tolerance-kubernetes-cluster.png#center)


### Master Components
When you install Kubernetes, the four components will be installed on the master node to manage and run the worker nodes.
I have presented the below diagram for the key components of master node.
![components of master node](/images/posts/understanding-the-basic-concepts-of-kubernetes-cluster/components-of-master-node.png#center)
#### API Server

The Kubernetes API server exposes the Kubernetes API to external users so that they can perform operations on the Kubernetes cluster. 
The API server processes REST operations, validates them, and updates the corresponding objects 
such as pods, services, replication controller, and deployments in etcd.

REST operations is done by using `cli` interface called `kubectl` (very small Go language binary) or UI interface.

As for example to list the pods in Kubernetes we can run below `kubectl` command : 

`kubectl get pods`

#### Scheduler
The scheduler is responsible for scheduling pods based on the configuration file. In the configuration file, you can define the number of CPU, memory, and other configurations. 
Once we pass the configuration file to the API server, the scheduler selects the best worker node to run the pods, based on resource availability on the worker nodes.

For example, let's say your cluster has two worker nodes: one with CPU utilization of 60% and another with CPU utilization of 30%. 
The scheduler will select the worker node with 30% CPU utilization to run the newly created pod.
#### Controller Manager
The Controller Manager is responsible for the overall health of the entire cluster. 
It ensures that worker nodes are up and running and that the correct number of pods are running. 
It detects changes in the cluster state and attempts to restore it as soon as possible.

For example, if a pod dies, it will detect the state change by reading the information stored in etcd and create a new pod as soon as possible.

#### etcd

etcd is the central distributed database, it stores the cluster information in key-value store such as its current state, desired state, configuration of resources, and runtime data, often referred to as the cluster's brain. 
Kubernetes interacts with etcd through its API server. It is the single source of truth for cluster information at any given point of time 

## Worker Node
A worker node is a virtual/physical server in data center or virtual machine running in the cloud where containers are deployed. 
The worker node is the machine where the actual user applications runs inside the pods.

### Worker Node Components
I have presented the below diagram for the key components of worker node.
![components of master node](/images/posts/understanding-the-basic-concepts-of-kubernetes-cluster/components-of-worker-node.png#center)
Every worker node has the following components:
#### Kubelet
The Kubelet is a primary node agent that runs on each worker node in the cluster.
Its primary responsibility is to look at the spec submitted to the API server on the
Kubernetes master and ensure that containers defined in 
the submitted spec file are running inside pods and healthy. If the Kubelet notices any issues while running pods, it tries to restart the pod on the same node.

If the issue is with the worker node itself, the Kubernetes 
master detects it and selects another suitable worker node in the cluster to run the pods.

#### Kube-proxy
Kube-proxy is a network proxy that runs on each node in the cluster.
It is responsible for managing network connectivity, maintaining network rules across all nodes, pods, and containers inside the cluster, and also exposing services to the outside world.
Kube-proxy is the core network component for entire Kubernetes cluster.

#### Container Runtime
The container runtime is the software application which runes inside the every worker 
node and is responsible for running containers. Kubernetes supports Docker, containerd, CRI-O, etc.

#### Pods
A pod is one of the most used terms in Kubernetes (k8s). 
It is the smallest unit of a k8s cluster that wraps the containers. 
Each pod consists of one or more containers. 
In most cases, one pod runs one container. 
Sometimes, there are more than one dependent containers running together inside a single pod, where one container helps the other container complete the job.

The simple relationship between a worker node, pod, and container:
Inside a worker node, there is at least one pod, and inside a pod, there is at least one container.

This post explains the basic concepts of a Kubernetes (k8s) cluster with a architecture diagram,
highlighting the roles of master nodes and worker nodes, along with key components like the API server, 
scheduler, controller manager, etcd, Kubelet, Kube-proxy, and container runtime.

Hope this post helped you to understand the Kubernetes architecture and its key components.

