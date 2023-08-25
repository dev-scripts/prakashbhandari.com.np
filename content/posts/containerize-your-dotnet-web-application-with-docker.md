---
title: "Containerize Your .NET 7.0 Web Application With Docker"
date: 2023-08-25T08:00:08+10:00
draft: false
showToc: true
TocOpen: true
tags : [
"Docker",
"Dot Net",
"Dot Net 7.0",
".NET Web Application"
]
categories : [
"Docker"
]
keywords: [
"Containerize Your .NET Web Application With Docker",
"How to Dockerize Your .NET Web Application",
"How to Dockerizing a Your .NET Web Application",
"Docker and Dot Net",
"Containerize Your .NET 7.0 Web Application With Docker"
]
cover:
    image: "images/posts/containerize-your-dotnet-web-application-with-docker/containerize-your-dotnet-web-application-with-docker.png"
    alt: "Containerize Your .NET 7.0 Web Application With Docker"
    caption: "Containerize Your .NET 7.0 Web Application With Docker"
    relative: false
    author: "Prakash Bhandari"
    description: "Dockerizing is the process of packing, deploying, and running applications using Docker containers.
    Docker is very popular among the developers."
---

Dockerizing is the process of packing, deploying, and running applications using Docker containers.
Docker is very popular among the developers. In this post, I am going to show you **"Containerize Your Dotnet Core Application With Docker"**.
for local development.

Before that, I will briefly define what is docker and .NET

## What Is Docker?
**[Docker](https://www.docker.com/)** is an open source tool that combines your application with all
the necessary dependencies and libraries as one portable package (docker image).
That package can be shared to any one and run by anyone without much worrying about the operating system.

## .NET

.NET (pronounced as "dot net"; formerly named .NET Core) is a free and open-source, managed computer software framework for Windows, Linux, and macOS operating systems. It is a platform independent and successor to .NET Framework
The project is mainly developed by Microsoft employees by way of the .NET Foundation, and released under an MIT License.


>Dockerizing is the process of packing, deploying, and running applications using Docker containers.

##  Prerequisites
Docker should be pre-installed in your machine.
If you don't have, please install form following link:
1. Install docker form : https://docs.docker.com/engine/install/


Dotnet application can be decorize in following steps.

### Step 1 : Create New .NET 7.0 Web Application

Either your can use existing .NET 7.0 web application or create new .NET 7.0 web application.

I will be creating small .NET 7.0 web application to access the default API end point provided by the framework.

I am using visual studio to create the Application to create .NET 7.0 web application.

By default, the app will give us a `WeatherForecastController.cs`

Here is the structure of my Dotnet application.

![Containerize Your .NET 7.0 web application With Docker](/images/posts/containerize-your-dotnet-web-application-with-docker/project-structure.png#center)

### Step 2 : Create `Dockerfile` file

Create a `Dockerfile` inside root of your project. 
This file should include following instruction.

```
# Stage 1: Build the application
FROM mcr.microsoft.com/dotnet/sdk:7.0 AS build
WORKDIR /app

# Copy the project file and restore dependencies
COPY ["dockerizeDotNet7App.csproj", "."]
RUN dotnet restore dockerizeDotNet7App.csproj

# Copy the rest of the application code
COPY . .

# Build the application
RUN dotnet build dockerizeDotNet7App.csproj -c Release -o /app/build

# Publish the application
RUN dotnet publish dockerizeDotNet7App.csproj -c Release -o /app/publish

# Stage 2: Create the final image
FROM mcr.microsoft.com/dotnet/aspnet:7.0 AS final
WORKDIR /app
EXPOSE 80

# Copy the published application from the build stage
COPY --from=build /app/publish .

# Set the entry point
ENTRYPOINT ["dotnet", "dockerizeDotNet7App.dll"]
```

### Step 3 - Add a `docker-compose.yml` file

Add a `docker-compose.yml` in your project root. Please check in the attached project structure image. This `docker-compose.yml` is not necessary, but it helps to build abd
run the docker image and container easily.
I have used following instruction in the docker file.

```yaml
version: '3.8'
services:
  web_app:
    build:
      dockerfile: Dockerfile
    ports:
      - "9000:80"
```

#### Port mapping

Port mapping is very important here step. In Stage 2 of `Dockerfile`, where I have open a port on the Docker container. You might be thinking port 80 is default or common port and should be open by default, But in docker, it is not. By default, Docker doesn’t allow inbound requests to your container unless you explicitly tell Docker to open a port.
In this project I am exposing port `80` internally in side Docker and mapped to port `9000` of host machine form `docker-compose.yml` file. So that I can run my application in `http://localhost:9000/`


### Step 4 - Build Docker Image
To build docker image, you have to run `docker compose build` command. This command will take few minutes to
pull the dotnet image, install dependencies, copy files, and create app image.

### Step 5 - Start the containers
Now, you can run `docker compose up` command. This command will start project in the container and
maps container's internal port `80` to external port `9000`.

Once `docker compose up` is successful, you can access your app locally via web browser `http://localhost:9000/WeatherForecast`
Here, `WeatherForecast` is the default API provided by .NET 7.0.

To stop the containers, press Ctrl+C in the terminal.

### Step 6 - Test the Application

You should be able to access your application locally at port number `9000`.
Y
ou can browse `http://localhost:9000/WeatherForecast` in your web browser, and you should be able to see below screen.

![How to Dockerize a React Application?](/images/posts/containerize-your-dotnet-web-application-with-docker/output.png#center)

## Conclusion

Finally, done. That’s it!

We have locally created .NET 7.0 web application, dockarize and run the app by using the docker-compose tool. Hope this steps helped you
to skill up.




