---
title: "How to Dockerize a React Application?"
date: 2023-01-05T13:01:39+11:00
draft: false
showToc: true
TocOpen: true
tags : [
"Docker",
"JavaScript",
"ReactJS"
]
categories : [
"Docker",
"ReactJS",
"JavaScript",
]
keywords: [
"How to Dockerize a React Application ?",
"How to Dockerize a React App",
"How to Dockerizing a React Application",
"How to Dockerizing a React App",
"Docker and React",
"Create React App",
"Dockerizing a React Application with Nginx",
"Multi stage docker build for a React Application with Nginx",
"Multi stage docker build for a React Application",
"Containerising React Application"
]
cover:
    image: "images/posts/how-to-dockerize-a-react-application/how-to-dockerize-a-react-application.png"
    alt: "How to Dockerize a React Application?"
    caption: "How to Dockerize a React Application?"
    relative: false
author: "Prakash Bhandari"
description: "Dockerizing is the process of packing, deploying, and running applications using Docker containers.
Docker is very popular among the developers."
---

Dockerizing is the process of packing, deploying, and running applications using Docker containers.
Docker is very popular among the developers. In this post, I am going to show you **"How to Dockerizing the React Application?"**.
for local development and production ready. 

Before that I will briefly define what is docker and react.

## What Is Docker?
**[Docker](https://www.docker.com/)** is an open source tool that combines your application with all
the necessary dependencies and libraries as one portable package (docker image). 
That package can be shared to any one and run by anyone without much worrying about the operating system.

## What is React ?
**[React](https://reactjs.org/)** or **[React.j](https://reactjs.org/)**  or **[ReactJS](https://reactjs.org/)**
a free, open-source and popular JavaScript library for building user interfaces. 
It is maintained by Meta and a community of individual developers and companies. 

If you want to know more about react place check the tutorial at https://reactjs.org/tutorial/tutorial.html

>Dockerizing is the process of packing, deploying, and running applications using Docker containers.

##  Prerequisites
Docker and Node should be pre-installed in your machine. 
If you don't have, please install form following link:
1. Install docker form : https://docs.docker.com/engine/install/
2. Install Node form : https://nodejs.org/en/download/

## Dockerizing an App
React application can be decorize in following steps. 

### Step 1 : Create New React Application 

Either your can use existing react application or create new react application. 
I will be creating small React App. 

You can create a new React app by running the command below in your terminal.

`npx create-react-app react-front-end`

React app will be created and all the files and folders will be stored inside ` react-front-end` folder
Here is the structure of the React App.

```
| - docker
| | - nginx
| | | - nginx.conf
| | - react
| | | - Dockerfile
| - public
| - src/
| | - App.js
| | - index.js
| - .gitignore
| - .env
| - package-lock.json
| - package.json
```

### Step 2 - Create a `Dockerfile`  and `nginx.conf` file 
Create a `Dockerfile` inside `docker/react` and `nginx.conf` inside `docker/nginx` folder.   

#### 1. Create `Dockerfile` file

Create a `Dockerfile` inside `docker/react`. This file should include following instruction. I am using multi-stage build. 
In **Stage 0** it will build and compile the frontend and in **Stage 1** copy built assets from `build-stage` image.
Here, I have used the **Nginx** image. Nowadays, **Nginx** is more or less the **"de facto standard"** for static content serving.

If you want to know more about **Nginx**  place check at https://www.nginx.com/

```
# Stage 0 :  "build-stage", based on Node.js, to build and compile the frontend
FROM node:16.3.0-alpine as build-stage
# set working directory
WORKDIR /app/

# install app dependencies
COPY package.json /
COPY package-lock.json /
RUN npm install --silent

# Copy files and folders to image
COPY . .

# compact to a small package 
RUN npm run build

# Stage 1 : 
FROM nginx:1.22-alpine

# Copy built assets from `build-stage` image
COPY --from=build-stage /app/build/ /usr/share/nginx/html

COPY docker/nginx/nginx.conf /etc/nginx/conf.d/default.conf

# Start nginx
CMD ["nginx", "-g", "daemon off;"]
```

#### 2. Create `nginx.conf` file

Create a `nginx.conf` inside `docker/nginx`. This file should include following instruction. 
You can add additional configuration if you need. But, you don't have to worry much about Nginx configuration.
The official Docker Nginx image will do all almost everything for you.

```
server {
  listen 80;
  location / {
    root /usr/share/nginx/html/;
    include /etc/nginx/mime.types;
    try_files $uri $uri/ /index.html;
  }
}
```

### Step 3 - Add a `docker-compose.yml` file

Add a `docker-compose.yml` in your project root. This `docker-compose.yml` is not necessary, but it helps to build abd 
run the docker image and container easily.
I have used following instruction in the docker file.

```yaml
version: '3.7'
services:
  app:
    container_name: react-front-end-app
    build:
      dockerfile: ./docker/react/Dockerfile
    ports:
      - 1111:80
    volumes:
      - '.:/app'
      - '.:/app/node_modules'
    networks:
      - local_network
networks:
  local_network:
    driver: bridge
```

In local development you don't need to use Nginx. But, it's better to use the Nginx regardless of environments.

If you don't want to use Nginx in your local development environment then you can replace `Stage 1`
form `Dockerfile` with following code and change 
the port number in `docker-compose.yml` file form `"1111:80"` to `"1111:3000"`

```yaml
# app runs on 3000 internally in to docker container
EXPOSE 3000

# start app
CMD ["npm", "start"]
```

### Step 3 - Add a `.dockerignore` file
Just like the `.gitignore` file, we need a `.dockerignore` 
file as well so that docker knows what to ignore in the build process. We don't need to include all files 
inside image. Image should be light.

Following files and folders should be included in side `.dockerignore` file.

```
node_modules
build
.dockerignore
docker-compose.yml
```
**Why we are not including these files and folders in image?**
1.  ***node_modules*** : `RUN npm install --silent`  from docker file will install `node_modules`  inside container. `package-lock.json` and `package.json` files will be used to install dependencies.
2. ***build***  : `RUN npm run build` from docker file will create build folder
3. ***.dockerignore*** and ***docker-compose.yml*** :  we don't need both files inside container.

### Step 4 - Build Docker Image
To build docker image, you have to run `docker compose build` command. This command will take few minutes to 
pull the node image, install dependencies, copy files, and create app image.

### Step 5 - Run React Application
Now, you can run `docker compose up` command. This command will start project in the container and 
maps container's internal port `80` to external port `1111`. Open your browser in

Once `docker compose up` is successful, you can access your React app locally via web browser `http://localhost:1111/`

### Step 6 - Test the Application

Your app should be able to access your application locally at port number `1111`. 
You can browse `http://localhost:1111/` in your web browser, and you should be able to see following screen.

![How to Dockerize a React Application?](/images/posts/how-to-dockerize-a-react-application/output.gif#center)

## Conclusion

Finally, done. That’s it! 

We have locally created React Application, dockarize and run the app. Hope this steps helped you 
to skill up. 

Also, ready for production with great performance. we should be thankful to Nginx.

Also, if you find any problem with app or any mistakes in this article, 
please post in the comment, it will automatically create a git issue.

[GitHub version of the article](https://github.com/dev-scripts/dockerize-react-app)


