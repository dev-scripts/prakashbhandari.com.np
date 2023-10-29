---
title: "Docker Commands Cheat Sheet"
date: 2023-10-25T10:04:16+11:00
showToc: true
TocOpen: true
tags : [
  "Docker",
]
categories : [
  "Docker",
]
keywords: [
  "Docker commands",
  "Docker cheat sheet",
  "Docker commands reference",
  "Docker command list",
  "Docker tutorial",
  "Docker commands for beginners",
  "Docker commands for containers",
  "Docker commands with examples",
  "Docker commands quick reference",
  "Docker command line reference",
  "Docker CLI commands",
  "Docker command guide",
  "Docker commands explained",
  "Docker commands for DevOps",
  "Essential Docker commands",
  "Docker container management",
  "Docker command syntax",
  "Docker commands for images",
  "Docker command reference sheet"
]
cover:
  image: "images/posts/docker-commands-cheat-sheet/docker-commands-cheat-sheet.png"
  alt: "Docker Commands Cheat Sheet"
  caption: "Docker Commands Cheat Sheet"
  relative: false
  author: "Prakash Bhandari"
  description: "Master Docker with our comprehensive Docker Commands Cheat Sheet. Quickly access essential Docker commands, examples, and tips for container management. Whether you're a Docker beginner or a seasoned pro, our cheat sheet is your go-to resource for streamlining container operations."
---

In this post, I've compiled a comprehensive list of docker commands that cover basic Docker commands,
Docker Compose, and Docker Registry.
For each command, I have added a brief explanation of its purpose and usage. 
Whether you're managing containers, orchestrating services with 
Docker Compose, or working with Docker Registry, this guide will be you a lot.

# Basic Docker Commands

1. Check Docker version

```
docker --version or docker -v
```

2. Display system-wide information about Docker

```
docker info
```

3. Download an image from Docker Hub.

```
docker pull <image_name>
```

4. List local Docker images.

```
docker images or docker image ls
```

5. List running containers

```
docker ps or docker container ls
```

6. List all containers (including stopped ones)

```
docker ps -a or docker container ls -a
```

7. Create and start a new container from an image.

```
docker run <options> <image_name>
```

# Docker Container Lifecycle Management Commands

1. Start a stopped container

```
docker start <container-name/id>
```

2. Stop a running container gracefully.

```
docker stop <container-name/id>
```

3. Forcefully stop a running container

```
docker kill <container-name/id>
```

4. Restart a container

```
docker restart <container-name/id>
```

5. Remove a stopped container

```
docker rm <container-name/id>
```

# Docker Volume Commands
1. Create a named volume

``` dockerfile
docker volume create <volume-name>
```

2. Mount a volume to a container

``` dockerfile
docker run -v <volume-name>:<container-path>
```
3. List volumes

```
docker volume ls
```

4. Remove a volume

```
docker volume rm <volume-name>
```

# Docker Network Commands

1. Create a user-defined network
```
docker network create <network-name>
```
2. List the docker networks.

```
docker network ls
```
3. Connect a container to a network,
```
docker network connect <network-name> <container-name/id>
```
4. Disconnect a container from a network
```
docker network disconnect <network-name> <container-name/id>
```
# Docker Image Commands

1. Build a Docker image from a Dockerfile
```
docker build -t <image-name> <path-to-Dockerfile>
```
2. Remove an image
```
docker rmi <image-name/id>
```
3. Remove all unused images

```
docker image prune
```
# Logs and debugging docker commands
1. View container logs
```
docker logs <container-name/id>
```
2. Start an interactive shell in a running container
```
docker exec -it <container-name/id> /bin/bash
```
3. Display real-time container resource usage.
```
docker stats <container-name/id>
```

# Cleanup commands
1. Remove all stopped containers, unused networks, and images
```
docker system prune
```
2. Remove all stopped containers.
```
docker container prune
```
3. Remove all unused images
```
docker image prune
```
4. Remove all unused volumes
```
docker volume prune
```

# Docker Registry/Hub Commands
1. Log in to a Docker registry/Docker Hub.
```
docker login
```
2. Push an image to a registry.
```
docker push <image-name>
```
3. Pull an image from a registry.
```
docker pull <image-name>
```
# Docker Composer Commands
1. Start services defined in a `docker-compose.yaml` file
```
docker-compose up
```
2.  Stop and remove services defined in a `docker-compose.yaml` file
```
docker-compose down
```
3.  List services in a `docker-compose.yaml` file and their status.
```
docker-compose ps
```
4. View logs for a specific service

```
docker-compose logs <service-name>
```
5. Run a command in a running service container
```
docker-compose exec <service-name> <command>
```

Understanding Docker, Docker commands, Docker Compose commands, and Docker Registry commands 
is essential for anyone working with containers. 
This post helps you with the knowledge and confidence to tackle Docker related tasks efficiently.