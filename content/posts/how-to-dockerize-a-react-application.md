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
"Create React App "
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
Before that I will briefly define what is docker and react.

## What Is Docker?
**[Docker](https://www.docker.com/)** is an open source tool that combines your application with all
the necessary dependencies and libraries as one portable package (docker image). 
That package can be shared to any one and run by anyone without much worrying about the operating system.
## What is React ?
**[React](https://reactjs.org/)** or **[React.j](https://reactjs.org/)**  or **[ReactJS](https://reactjs.org/)**
a free, open-source and popular JavaScript library for building user interfaces. 
It is maintained by Meta and a community of individual developers and companies. 

## What is Dockerizing?
Dockerizing is the process of packing, deploying, and running applications using Docker containers.

##  Prerequisites
Docker and Node should be pre-installed in your machine.

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
| - public
| - src/
| | - App.js
| | - index.js
| - .gitignore
| - .env
| - package-lock.json
| - package.json
```

### Step 2 - Add a `Dockerfile` file 
Add a `Dockerfile` in your project. This `Dockerfile` should include all the 
instruction to decorize the React App. I have used following instruction in the docker file.

```
# pull official base image
FROM node:16.3.0-alpine
# set working directory
WORKDIR /app

# install app dependencies
COPY package.json ./
COPY package-lock.json ./
COPY public/ ./public/
RUN npm install --silent

# add app
COPY src/ ./src/

RUN npm run build

# app runs on 3000 internally in to docker container
EXPOSE 3000

# start app
CMD ["npm", "start"]
```

### Step 3 - Add a `docker-compose.yml` file

Add a `docker-compose.yml` in your project root. This `docker-compose.yml` is not necessary but it helps to build abd 
run the docker image and container easily.
I have used following instruction in the docker file.

```yaml
version: '3.7'
services:
  app:
    container_name: react-front-end
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - '1111:3000'
    volumes:
      - '.:/app'
      - '/app/node_modules'
```

### Step 3 - Add a `.dockerignore` file
Just like the `.gitignore` file, we need a `.dockerignore` 
file as well so that docker knows what to ignore in the build process.
In our case we have only copied necessary files in `Dockerfile`.
But, if you want you can include following details in `.dockerignore` file.

```
node_modules
build
.dockerignore
Dockerfile
```
### Step 4 - Run Docker Build
You have to run `docker compose build` command. This command will take few minutes to 
pull the node image, install dependencies, copy files, and create app image.

### Step 5 - Run React Application
Now, you can run `docker compose up` command. This command will start the container and 
maps internal port `3000` to external port `1111`.

Once `docker compose up` is successful, you can access your React app locally via web browser `http://localhost:1111/`

### Step 6 - Check in Browser

Your app should be running locally at port number `1111`. 
You can browse `http://localhost:1111/` in your web browser, and you should be able to see following screen.

![Reactive Resume builder](/images/posts/how-to-dockerize-a-react-application/output.gif#center)


## Conclusion

We have locally created React Application, dockarize and run the app. Hope this steps helped you 
to skill up.

Same app can be run in the production. But, you need to add the nginx server. 
I will write separate blog post for that or I will update this post in the future.
