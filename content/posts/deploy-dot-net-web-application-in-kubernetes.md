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
  "Docker and Kubernetes"
]
cover:
  image: "images/posts/deploy-dot-net-web-application-in-kubernetes/deploy-net-web-application-in-kubernetes.png"
  alt: "Deploy .NET Web Application in Kubernetes"
  caption: "Deploy .NET Web Application in Kubernetes"
  relative: false
  author: "Prakash Bhandari"
  description: "In this article, I will explain the basics of how to deploy a .NET Web application in Kubernetes."
---

## Introduction 
In this article, I will explain the basics of how to deploy a .NET Web application in Kubernetes.
I will use minikube to deploy app in the local machine.

Either your can use existing .NET Web Application or create new .NET Web Application and containerize it.

I am going to use the app that was created in my previous
**[Containerize Your .NET 7.0 Web Application With Docker](https://www.prakashbhandari.com.np/posts/containerize-your-dotnet-web-application-with-docker/)**

Kubernetes(k8s) can only deploy containers because Kubernetes only knows how to deploy containers.

In, previous article I have already containerize the application using Docker.

There are many other tools to containerize the application like **LXC**, **Podman**, **Containerd** etc, 
But Docker is the most popular tool to create and run containers.

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

I will deploy an application in `minikube` using Kustomize on a Kubernetes cluster

Kustomize is an open-source configuration management tool for Kubernetes.

It allows you to define and manage Kubernetes objects such as deployments, Daemonsets, services, configMaps, etc for multiple environments in a declarative manner without modifying the original YAML files.

The kustomize module is built into kubectl. You can use customize directly via kubectl. You can verify it using the following command.

You can verify with below command:
```
kubectl kustomize --help
```
I will not go in details about Kustomize. I am using it here because it makes our deployment very easy
or we can simply deploy image with single command in minikube.

Let me create following folders and files in the root of the project

```
| - k8s
| | - base
| | | - deployment.yaml
| | | - ingress.yaml
| | | - service.yaml
| | | - kustomization.yaml
| | - overlays
| | | - kustomization.yaml
```

Let me simplify this with the process with below diagram.

![customized manifest](/images/posts/deploy-dot-net-web-application-in-kubernetes/customized-manifest-arc.png#center)

Here, we are deploying into single minikube cluster. 

Suppose you want to deploy applications to Kubernetes and you have multiple environments i.e. dev, uat, prod etc. 
In each environment, you might have different configurations for the deployments.

In this case you required more configurations on top of the base YAMLs as per the environment requirements.

### Deployment (deployment.yaml)
A deployment allows you to describe an application's life cycle, 
such as which images to use for the app, the number of pods that we want. Update pods and replica sets, rollback to previous deployment versions, 
scale a deployment , pause or continue a deployment, etc

We define set of instructions in a YAML file. ie `deployment.yaml`

{{< github-code-snippets e49a0fe4dd5ef1b6856d70f2177ccbb6 >}}

### Service (service.yaml)
Services provide a way to expose your application's Pods to the network 
or other services within the cluster, and they play a very important role in enabling communication between different parts of your application.

Here is a `service.yaml` file that we are using:

{{< github-code-snippets 9cd5c91abc9ec1a2aa145081eed23cbf >}}

### Kustomization in Base folder (kustomization.yaml)
The `kustomization.yaml` file is the main file used by the Kustomize tool and reside in `base` folder.

This file contains a list of all the Kubernetes resources (YAML files) that should be managed by Kustomize.


Here is a `kustomization.yaml` file that we are using:

{{< github-code-snippets 7f52357de8527b097dc8839413dba539 >}}

### Kustomization in Overlays folder (kustomization.yaml)

It  contains all the customizations that we want to apply to generate the customized manifest and reside in `overlays` folder.

Here is a `kustomization.yaml` file that we are using:

{{< github-code-snippets da75cb22cb9a8d29060124777266e918 >}}

### Deploy your app in Kubernetes
Hope you have `kubectl`  cli. Now, you can run below command to deploy app in the  Kubernetes cluster.

`kubectl apply -k ./k8s/overlays`

Once deployment is successful you can verify that your pods is running by running below command

```
kubectl get pods
```

### Accessing an app via browser

To check the app via browser or form any HTTP client. First you need to find 
the URL in which app is accessible.

In minikube you can run following command. 

```minikube service --url=true dotnet7-web-app```

The above command will give the base URL. Something like:

```
http://127.0.0.1:63757
```

Now, you are ready to access you app via browser. For my app I can use
`http://127.0.0.1:63660/WeatherForecast` to see the response.

![output](/images/posts/deploy-dot-net-web-application-in-kubernetes/output.png#center)

### Create an Ingress and use custom domain to access App via browser

Ingress is important in k8s it exposes `HTTP` and `HTTPS` routes from outside the cluster to services within the cluster.
Traffic routing is controlled by rules defined on the Ingress resource.

The Ingress is the equivalent of a reverse proxy in Kubernetes.

![Ingress](/images/posts/deploy-dot-net-web-application-in-kubernetes/ingress-1.png#center)

Everytime running this command ```minikube service --url=true dotnet7-web-app``` and getting 
the app running URL is tedious.

So, I have already added the `ingress.yml` file in **base** folder. 

Here is a `ingress.yaml` file that we are using:

{{< github-code-snippets b3e88371fff7677b1c0d37be060adbab >}}

In this file I have added `- host: kubernete.local` line
which is the custom domain. App should be accessible in custom domain `http://kubernete.local`

Minikube comes with the Nginx as Ingress controller, and you can enable it with below command:

```minikube addons enable ingress```

It may take few minutes for Minikube to download and install Nginx as an Ingress, please wait.

Once `ingress` is enabled, you can find the Minikube IP with below command: 

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

In this article, I explained the basics of how to deploy a .NET Web application in Kubernetes.
We created image of app using docker, push it to the Docker Hub and deploy in minikube.

We also used **Kustomize** configuration management tool for Kubernetes to simplify the deployment process.



