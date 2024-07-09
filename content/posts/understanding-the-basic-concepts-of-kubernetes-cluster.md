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
  "Kubernetes cluster architecture", 
  "Kubernetes master and worker nodes",
  "Understanding master and worker nodes in Kubernetes",
  "Kubernetes basic concepts", 
  "Understanding Kubernetes", 
  "Kubernetes for beginners",
  "beginners guide for Kubernetes(k8s) Cluster",
  "Kubernetes cluster components", 
  "How Kubernetes works", 
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

As a beginner, understanding a Kubernetes (k8s) cluster might be a little bit complicated. 
In this blog post, I will try to simplify the basic concepts  and components of a Kubernetes (k8s) cluster.

# What is a Kubernetes Cluster?
The simplest definition of a Kubernetes(k8s) cluster is a set of nodes that run containerized applications. Kubernetes cluster has two types on nodes
1. Master Node
2. Worker/Slave Node
   
A cluster can be simple, with one master node and one worker node, or it can be complex, with many master nodes and many worker nodes.

I have presented a Kubernetes cluster architecture diagram, this diagram presented a Kubernetes cluster architecture with two master nodes
and four worker nodes. Each node is running four pods. Some pods are running one container
inside, while others are running more than two containers inside the pod.

![Kubernetes Cluster Architecture](/images/posts/understanding-the-basic-concepts-of-kubernetes-cluster/understanding-the-basic-concepts-of-kubernetes-cluster.png#center)


## Master Node
The master node is the control plane of Kubernetes which is responsible for controlling and managing a set of worker nodes globally within the cluster. 
It maintains the overall state of the cluster, coordinating tasks such as scheduling applications and managing cluster-wide resources.
### Master Components
A master node has the following components to help manage worker nodes:
#### API Server

The Kubernetes API server exposes the Kubernetes API to external users so that they can perform operations on the Kubernetes cluster. 
The API server processes REST operations, validates them, and updates the corresponding objects in etcd.

#### Scheduler
Scheduler is responsible to select the best node to run the pods. 
Worker node is selected based on resource availability in the worker node.

For example, let's say your cluster has two worker nodes: one with CPU utilization of 60% and another with CPU utilization of 30%. 
The scheduler will select the worker node with 30% CPU utilization to run the newly created pod.
#### Controller Manager
t detects changes in the cluster state and tries to restore it as soon as possible. 
For example, if a pod dies, it will detect the state change by reading the information stored in etcd and create a new pod as soon as possible.
#### etcd
etcd is the key-value store for the cluster state, often referred to as the cluster's brain. 
It stores the configuration information of the Kubernetes cluster and records changes in the cluster, 
such as which pods are scheduled and which pods die.

## Worker/Slave Node
A worker node, also known as a slave node, is the machine where applications run or where application pods run. 
A single node can run more than one pod. 
Every node should have installed  three software applications: Kubelet, Kube-proxy, and Container Runtime.
### Node Components
A worker/slave node has the following components:
#### Kubelet
The Kubelet is a software application that runs on each node in the cluster. 
It interacts with both the node and the containers. 
It's responsibilities include starting the pod with a container inside 
and assigning resources such as CPU, memory, and storage from the node to the pod.
#### Kube-proxy
Kube-proxy is a network proxy that runs on each node in the cluster. 
It is responsible for managing network connectivity and maintaining network rules across nodes, enabling communication to your pods from both inside and outside the cluster.
Kube-proxy implements the Kubernetes Service concept across every node in the cluster.
#### Container Runtime
The container runtime is the software application which runes inside the every worker 
node and is responsible for running containers. Kubernetes supports Docker, containerd, CRI-O, etc.

This post explains the basic concepts of a Kubernetes (k8s) cluster with a Kubernetes architecture diagram,
highlighting the roles of master nodes and worker nodes, along with key components like the API server, 
scheduler, controller manager, etcd, Kubelet, Kube-proxy, and container runtime.

