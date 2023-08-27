---
title: "Deploy .NET Web Application In Kubernetes Using Kustomize Tool"
date: 2023-08-27T08:57:55+10:00
showToc: true
TocOpen: true
tags : [
  "Docker",
  "dotNet",
  "k8s",
  "dotNET Web Application",
  "C Sharp",
  "Kubernetes",
  "Kustomize"
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
  "Deploy .NET Web Application In Kubernetes Using Kustomize Tool",
  "Deploy Web App In Kubernetes Using Kustomize Tool",
  "Deploy Web App In Kubernetes"
]
cover:
  image: "images/posts/deploy-dot-net-web-application-in-kubernetes-using-kustomize-tool/deploy-dot-net-web-application-in-kubernetes-using-kustomize-tool.png"
  alt: "Deploy .NET Web Application in Kubernetes"
  caption: "Deploy .NET Web Application in Kubernetes"
  relative: false
  author: "Prakash Bhandari"
  description: "In this article, I will explain the basics of how to deploy a .NET Web application in Kubernetes by using Kustomize Tool"
---

## Introduction 
In this article, I will explain the basics of how to deploy a .NET Web application in Kubernetes by using Kustomize Tool.
I will use minikube to deploy app in the local machine.

I have created the deployment files and image in previous post. I will use the same artifacts that are used in the article.
**[Deploy .NET Web Application In Kubernetes](https://www.prakashbhandari.com.np/posts/deploy-dot-net-web-application-in-kubernetes/)**

## Kustomize Tool

Kustomize is an open-source configuration management tool for Kubernetes.

It allows you to define and manage Kubernetes objects such as deployments, services, jobs, configMaps, etc for multiple environments in a declarative manner without modifying the original YAML files.

The kustomize module is built into kubectl. You can use customize directly via kubectl. 

You can verify with below command:
```
kubectl kustomize --help
```
Kustomize has two fundamental concepts:

### 1  Base
**Base** are the reusable files(YAML files) across all environments

### 2. Overlays
**Overlay** also called patches. These files are environment specific files.

Kustomize will generate final customized manifest from **Base** files and **Overlay** files as shown in the below diagram.

![customized manifest](/images/posts/deploy-dot-net-web-application-in-kubernetes-using-kustomize-tool/customized-manifest.png#center)

If you want to deploy app into multiple environments i.e. dev, uat and prod.
The common files are reside in base folder and environment specific files will reside in
environment specific folders. Like in below example:

```
| - k8s
| | - base
| | | - deployment.yaml
| | | - ingress.yaml
| | | - service.yaml
| | | - kustomization.yaml
| | - overlays
| | | - dev
| | | | - kustomization.yaml
| | | - dev
| | | | - kustomization.yaml
| | | - prod
| | | | - kustomization.yaml
```
Below diagram shows that above base and overlays for dev, uat and prod  will generate final customized manifest and 
deploy in each environment.

![customized manifest](/images/posts/deploy-dot-net-web-application-in-kubernetes-using-kustomize-tool/example-of-customized-manifest-for-more-than-one-environment.png#center)


## Deploying Your App in Kubernetes Using Kustomize Tool

I will deploy an application in `minikube` using Kustomize on a Kubernetes cluster with the help of Kustomize.

Let me create following folders and files in the root of the project. That was discussed in **[Deploy .NET Web Application In Kubernetes](https://www.prakashbhandari.com.np/posts/deploy-dot-net-web-application-in-kubernetes/)**

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

![customized manifest](/images/posts/deploy-dot-net-web-application-in-kubernetes-using-kustomize-tool/example-of-customized-manifest.png#center)

Here, we are deploying into single minikube cluster. 

Suppose you want to deploy applications to Kubernetes and you have multiple environments i.e. dev, uat, prod etc. 

In each environment, you might have different configurations for the deployments.

In this case you required more configurations on top of the base YAMLs as per the environment requirements.

### 1. Create Deployment
 First, step is to create deployment file. I have already discussed what is [deployment](https://www.prakashbhandari.com.np/posts/deploy-dot-net-web-application-in-kubernetes/#1-deployment-deploymentyaml) in previous post. 
 
Create `deployment.yaml` file like below:

{{< github-code-snippets e49a0fe4dd5ef1b6856d70f2177ccbb6 >}}

### 2. Create Service 

First, step is to create Service file. I have already discussed what is [Service](https://www.prakashbhandari.com.np/posts/deploy-dot-net-web-application-in-kubernetes/#2-service-serviceyaml) in previous post.

Create `service.yaml` file like below:
{{< github-code-snippets 9cd5c91abc9ec1a2aa145081eed23cbf >}}

### 3. Create Kustomization in Base folder (kustomization.yaml)
The `kustomization.yaml` file is the main file used by the Kustomize tool and reside in `base` folder.

This file contains a list of all the Kubernetes resources (YAML files) that should be managed by Kustomize.

Here is a `kustomization.yaml` file that I am using:

{{< github-code-snippets 7f52357de8527b097dc8839413dba539 >}}

### 4. Kustomization in Overlays folder (kustomization.yaml)

It  contains all the customizations that we want to apply to generate the customized manifest and reside in `overlays` folder.

Here is a `kustomization.yaml` file that I am using:

{{< github-code-snippets da75cb22cb9a8d29060124777266e918 >}}

### 5 Deploy your app in Kubernetes

Now, you can run below command to deploy app in the  Kubernetes cluster.

`kubectl apply -k ./k8s/overlays`

Once deployment is successful you can verify that your pods is running by running below command

```
kubectl get pods
```

### 6. Accessing an app via browser

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

![output](/images/posts/deploy-dot-net-web-application-in-kubernetes-using-kustomize-tool/output.png#center)

### 7. Create an Ingress and use custom domain to access App via browser

Ingress is important in k8s it exposes `HTTP` and `HTTPS` routes from outside the cluster to services within the cluster.
Traffic routing is controlled by rules defined on the Ingress resource.

The Ingress is the equivalent of a reverse proxy in Kubernetes.

![Ingress](/images/posts/deploy-dot-net-web-application-in-kubernetes-using-kustomize-tool/ingress-1.png#center)

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

![output](/images/posts/deploy-dot-net-web-application-in-kubernetes-using-kustomize-tool/output2.png#center)

If you are using **M1 with the docker driver**. You need to enable minikube tunnel. Keep in mind that your `etc/hosts` file needs to map to `127.0.0.1`, instead of the output of `minikube ip` or `kubectl get ingress`

## Conclusion

In this article, I explained the basics of how to deploy a .NET Web application in Kubernetes.
We created image of app using docker, push it to the Docker Hub and deploy in minikube.

We also used **Kustomize** configuration management tool for Kubernetes to simplify the deployment process.



