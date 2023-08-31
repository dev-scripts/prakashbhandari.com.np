---
title: "Deploy .NET Web Application In Kubernetes"
date: 2023-08-26T08:57:55+10:00
showToc: true
TocOpen: true
tags : [
  "Docker",
  "dotNet",
  "k8s",
  "dotNET Web Application",
  "C Sharp",
  "Kubernetes"
]
categories : [
  "Docker",
  "C Sharp",
  "Kubernetes"
]
keywords: [
  "Deploy .NET Web Application in Kubernetes",
  "Deploy .NET Web Application in k8s",
  "Docker and Kubernetes",
  "Kubernetes deployment",
  "NET Web application deployment",
  "Minikube deployment",
  "Containerization with Docker",
  "Kubernetes basics", 
  "Docker image creation",
  "Container orchestration",
  "Kubernetes tutorial",
  "Kubernetes for .NET",
  "Microservices deployment",
  "Kubernetes scalability",
  "Kubernetes services",
  "Ingress controllers",
  "Local Kubernetes development",
  "DevOps with Kubernetes",
  "Docker registry",
  "Kubernetes best practices",
  ".NET application containerization",
  "Kubernetes cluster management",
  "Deployment strategies in Kubernetes",
]
cover:
  image: "images/posts/deploy-dot-net-web-application-in-kubernetes/deploy-net-web-application-in-kubernetes.png"
  alt: "Deploy .NET Web Application in Kubernetes"
  caption: "Deploy .NET Web Application in Kubernetes"
  relative: false
  author: "Prakash Bhandari"
  description: "Learn how to deploy and scale your .NET Web applications effectively using Kubernetes and Docker. This comprehensive guide covers the basics of containerization, Kubernetes deployment strategies, and utilizing tools like Minikube for local development. Explore the power of Kubernetes services, ingress controllers, and best practices for deploying resilient and highly available applications. Elevate your DevOps game with this step-by-step tutorial on deploying .NET applications in a Kubernetes environment."
---

## Introduction 
In this article, I will explain the basics of how to deploy a .NET Web application in Kubernetes. I will use Minikube to deploy the app on the local machine.

You can either use an existing .NET Web Application or create a new .NET Web Application and containerize it.

I am going to use the app that was created in my previous article.

I've made some minor adjustments for clarity and proper capitalization.
**[Containerize Your .NET 7.0 Web Application With Docker](https://www.prakashbhandari.com.np/posts/containerize-your-dotnet-web-application-with-docker/)**

Kubernetes (k8s) can only deploy containers because it only knows how to deploy containers.

In the previous article, I have already containerized the application using Docker.

There are many other tools to containerize the application, such as LXC, Podman, Containerd, etc.
However, Docker is the most popular tool for creating and running containers.

## Prerequisites
You need to install following tools in your machine:

1. [Docker](https://docs.docker.com/get-docker/)
2. [Kubectl](https://kubernetes.io/docs/tasks/tools/) 
3. [Minikube](https://github.com/kubernetes/minikube)

## Create Docker Image 
Fist, step to deploy app in Kubernetes is build the image of web app that you want to run in Kubernetes.

I am building image form **[Containerize Your .NET 7.0 Web Application With Docker](https://www.prakashbhandari.com.np/posts/containerize-your-dotnet-web-application-with-docker/)**

Go inside project folder.

```
cd dockerizeDotNet7App/dockerizeDotNet7App 
```

Run command in terminal to build image:
```
docker build -t dockerizedotnet7app-web_app .
```

## Sharing Docker image with a registry

There are many registry to publish docker images. 
Here, I am using [Docker Hub](https://hub.docker.com/) to publish image publicly.
### Login to Docker Hub
Use below command to login to the Docker Hub. This command will help you to login in the Docker Hub.
```
docker login
```
### Push the Image to the Registry:
Push the image to the registry:
```
docker push <your-registry-username>/<your-repository-name:tag>
```

In my case, my docker push command will be like:

```
docker push thebhandariprakash/dockerizedotnet7app-web_app
```


## Deploying Your App in Kubernetes

I will deploy an application in Mac OS `minikube` local development Kubernetes cluster.


### 1. Deployment (deployment.yaml)
A deployment allows you to describe an application's life cycle, 
such as which images to use for the app, the number of pods that we want. Update pods and replica sets, rollback to previous deployment versions, 
scale a deployment , pause or continue a deployment, etc

We define set of instructions in a YAML file. ie `deployment.yaml`

{{< github-code-snippets e49a0fe4dd5ef1b6856d70f2177ccbb6 >}}

Now, you can apply the `deployment.yaml` file by using below command:

```
kubectl apply -f deployment.yaml
```
Once, deployment is created you will get `deployment.apps/web-app created` success message.

To verify, you can run the below command, which will list the pods. 

```
 kubectl get pods
```

The number of pods is depend upon the replicas value in `deployment.yaml` file `replicas: 1`.
In you case replicas value is `1`.

```
NAME                      READY   STATUS              RESTARTS   AGE
web-app-586b96bbb-2rjkf   1/1     Running             0          10s
```

Even if you delete pods with below command. It will spin up another pod immediately. 
```
kubectl delete pod <pod name>

Example : 
kubectl delete pod web-app-586b96bbb-2rjkf
pod "web-app-586b96bbb-2rjkf" deleted
```

Again, if you run `kubectl get pods`, you will see new pod.

### 2. Service (service.yaml)
Services provide a way to expose your application's Pods to the network 
or other services within the cluster, and they play a very important role in enabling communication between different parts of your application.

Here is a `service.yaml` file that I am using:

{{< github-code-snippets 9cd5c91abc9ec1a2aa145081eed23cbf >}}

Now, you can create the service by using `service.yaml`. If we run below command it will crate service:

```
kubectl create -f service.yaml
```
Once, service is created you will see `service/web-app created` success message.

To verify, you can run the below command, which will list the pods.

```
 kubectl get services
```
Now, you can see created service in your terminal.
```
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP        41h
web-app      NodePort    10.96.157.175   <none>        80:31747/TCP   4s
```

### 3. Accessing an app via browser

To check the app via browser or form any HTTP client. First you need to find 
the URL in which app is accessible.

In minikube you can run following command. 

```minikube service --url=true dotnet7-web-app```

The above command will give the base URL. Something like:

```
http://127.0.0.1:636660
❗  Because you are using a Docker driver on darwin, the terminal needs to be open to run it.
```

Now, you are ready to access you app via browser. For my app I can use
`http://127.0.0.1:63660/WeatherForecast` to see the response.

![output](/images/posts/deploy-dot-net-web-application-in-kubernetes/output.png#center)

### 4. Create an Ingress and use custom domain to access App via browser

Ingress is important in k8s it exposes `HTTP` and `HTTPS` routes from outside the cluster to services within the cluster.
Traffic routing is controlled by rules defined on the Ingress resource.

The Ingress is the equivalent of a reverse proxy in Kubernetes.

![Ingress](/images/posts/deploy-dot-net-web-application-in-kubernetes/ingress-1.png#center)

Everytime running this command ```minikube service --url=true dotnet7-web-app``` and getting 
the app running URL is tedious.

So, I have created the `ingress.yml` file. 

Here is a `ingress.yaml` file that I am  going to use:

{{< github-code-snippets b3e88371fff7677b1c0d37be060adbab >}}

In this file I have added, `host` as `kubernete.local` ie. `- host: kubernete.local`.
Which is the custom domain. App should be accessible in this custom domain `http://kubernete.local`

By default, minikube comes with the Nginx as Ingress controller, and you can enable it with below command:

```minikube addons enable ingress```

To enable ingress addons Minikube will take few minutes because it takes time to download and install Nginx as an Ingress.

Once ingress addons is ready, we can create Ingress with below command:
```
kubectl create -f ingress.yaml
```

Now, you can find the Minikube IP with below command: 

```minikube ip```

Let's say you have your minikube IP address `192.168.49.2`

Now, you need add my host names to `/etc/hosts`

```
192.168.49.2   kubernete.local
```

If you try `http://kubernete.local/WeatherForecast` in your browser, it should connect to app and give the result back.

Here is my output form custom domain `http://kubernete.local/WeatherForecast`

![output](/images/posts/deploy-dot-net-web-application-in-kubernetes/output2.png#center)

If you are using **M1 with the docker driver**. You need to enable minikube tunnel. Keep in mind that your `etc/hosts` file needs to map to `127.0.0.1`, instead of the output of `minikube ip` or `kubectl get ingress`
This is an very important.

## Conclusion

This article has provided a  guide to deploying a .NET Web application in Kubernetes. Throughout the process, I've covered essential steps from creating Docker images to setting up Kubernetes deployments and services. By utilizing tools like Minikube, Docker, and Kubernetes, I've successfully containerized and deployed the application for local Minikube Kubernetes. Additionally, I have explored advanced concepts such as creating an Ingress to enable custom domain access.