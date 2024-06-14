---
title: "Implementing Logging Using Node.Js, Elasticsearch, Kibana and Docker"
date: 2024-06-10T16:23:03+10:00
showToc: true
TocOpen: true
tags : [
  "System Design",
  "Software Development",
  "JavaScript",
  "Docker",
]
categories : [
  "System Design",
  "JavaScript",
  "Docker",
  "Software Development",
]
keywords: [
  "Logging and monitoring",
  "Node.js logging with Elasticsearch and Kibana", 
  "Dockerize Node.js app with Elasticsearch",
  "Kibana log visualization Node.js",
  "Node.js logging tutorial with Docker", 
  "Elasticsearch and Kibana integration with Node.js",
  "Real-world logging setup Node.js Elasticsearch", 
  "Monitor Node.js app logs Kibana", 
  "Set up Elasticsearch and Kibana for Node.js", 
  "Docker compose Elasticsearch Kibana Node.js", 
  "Winston logging Node.js Elasticsearch tutorial",
  "Node.js logging and Elastic stack integration",
  "Application Logging and its importance",
  "Store Logs on Elasticsearch using Winston and Nodejs: Elasticsearch, Kibana",
  "Implementing Logging Using Node.Js, Elasticsearch, Kibana and Docker",
]
cover:
    image: "images/posts/implementing-logging-using-node-js-elasticsearch-kibana-and-docker/implementing-logging-using-node-js-elasticsearch-kibana-and-docker.svg" # image path/url
    alt: "Implementing Logging Using Node.Js, Elasticsearch, Kibana and Docker"
    relative: false
author: "Prakash Bhandari"
description: "Logging and monitoring play a very important role in software development. They help to identify bugs and understand the application's health."
---

Logging play a very important role in software development. It helps monitoring, troubleshooting, debugging, event tracing, request tracing, security, and also can be used by Business Intelligence(BI) for reporting purpose.

Logging has numerous benefits. But in this blog post, we will focus on building a real-world example to store application logs in Elasticsearch via a Node.js app  by using the Winston NPM package and visualize them using Kibana. Additionally, we will dockerize the entire application.

# Prerequisite 
Following tools need to be installed in your machine.
1. Docker  (https://www.docker.com/products/docker-desktop/)
2. Node (https://nodejs.org/en/download/prebuilt-installer)
3. npm (Node Package Manager): Included with Node.js installation

# Create a Node.js App and install required packages
Node.js an asynchronous event-driven JavaScript runtime, which is designed to build scalable network applications.

You many already know how to create node app. But I will guide you to bootstrap basic node app. 

I will show you the project structure that I have set up locally. Here is the screenshot of the project structure: 

![project structure](/images/posts/implementing-logging-using-node-js-elasticsearch-kibana-and-docker/project-structure.png#center)

Here are the steps to create a Node.js app.

Create a new folder and run below command to initiate the node.js app. 
```
 npm init
```
 `npm init` will generated `package.json` file in your Node.js project folder.

During initialization, npm ask you number of questionst to collect the data.

The questions and their default answers are listed below:

**Packet name**: You can add your own project name instead of the default suggestion.

**Description**: You can give a brief summary of your project.

default dccess point `index.js` or you can change it to `app.js`. This is the main file that will be run when your project is launched.

**Git repository**: You can leave default or use use the URL of the repository.

**Author:** You can enter  project’s author’s name.

once all above steps are completed you will see `package.json` file inyour project folder.

If you run `npm install` then it generates the `package-json.lock` file.

```
npm install express
```

Create a file `index.js` file in the root of the project and add below code in the file

```js
app.get('/', function (req, res) {
  res.send('Hello World.');
});

app.listen(8000, function () {
  console.log('Listening on port 8000!');
});
```
Now, you can browse at `http://localhost:8000/` leter on the port number will be different once we dockrize the app.

# Dockerize the App
Docker is an open source tool that combines your application with all the necessary dependencies and libraries as one portable package (docker image). That package can be shared to any one and run by anyone without much worrying about the operating system. In this post we are using Docker to combines node app, Elasticsearch, and Kibana. 

In this post, I am not going to explain how Docker works. I assume that you are familiar with Docker, and will directly jump into the implementation. To Dockerize the the application, first step is to create `Dockerfile` with belwo defination. I am placing all the files in the root of the project.

```js
FROM node:20
WORKDIR /app
COPY package.json .
RUN npm install
COPY . .
EXPOSE 8000 //docker node app internal port
CMD npm start
```

Second sept is to create `docker-compose.yml` file with below defination. 
```js
version: '3.7'
services:
  app:
    build: .
    container_name: app-logging
    restart: unless-stopped
    ports:
      - '3002:8000'
    stdin_open: true
    tty: true
    networks:
      - elasticsearch-kibana-node-js-logging
volumes:
  data_es:
    driver: local
networks:
  elasticsearch-kibana-node-js-logging: null
  ```
  Now, you can run below command to start the app or you can run later once everyting is ready.

  ```
  docker compose up
  ```
 Above command will take few minute to build the docker image and run the container. Once, docker container is up and running the app can be browsed at port number `3002` (`http://localhost:8000/` ) because internal docker port `8000` is bind to external host machine `3002` port number.

 # Setting up Elasticsearch

 Elasticsearch is a distributed, RESTful search and analytics engine which can be used for many purpose. It can centrally stores all your data for lightning fast search, fine‑tuned relevancy, and powerful analytics that scale with ease. In this post we are using elasticsearch to store applocation's logs.

 Add below configuration to the `docker-compose.yml` file so that when you run the `docker compose up` command it will pull image form docker hub and build elasticsearch container in your machine within same network where node app container is running.
```js
elasticsearch:
    image: 'docker.elastic.co/elasticsearch/elasticsearch:8.10.2'
    container_name: elasticsearch-logging
    volumes:
      - 'data_es:/usr/share/elasticsearch/data'
    environment:
      - discovery.type=single-node
      - CLI_JAVA_OPTS=-Xms2g -Xmx2g
      - bootstrap.memory_lock=true
      - xpack.security.enabled=false
      - xpack.security.enrollment.enabled=false
    ports:
      - '9300:9200'
    networks:
      - elasticsearch-kibana-node-js-logging
```
Elasticsearch container will be running at port number `9300` of host machine. You can browse at `http://localhost:9300/` 

# Setting up Kibana
Run data analytics at speed and scale for observability, security, and search with Kibana. Powerful analysis on any data from any source, from threat intelligence to search analytics, logs to application monitoring, and much more.
In this post we are using the visulize and search the applaction's logs.

Add the following configuration to the `docker-compose.yml` file to setup the Kibana in the project. So, that when you run the `docker compose up` command, it will pull the image from Docker Hub and build the Kibana container in your machine within same network where node app container and elasticsearch containers are running.

```js 
kibana:
    image: 'docker.elastic.co/kibana/kibana:8.10.2'
    container_name: kibana-logging
    environment:
      - 'ELASTICSEARCH_HOSTS=http://elasticsearch:9200'
      - 'XPACK_ENCRYPTEDSAVEDOBJECTS_ENCRYPTIONKEY=d1a66dfd-c4d3-4a0a-8290-2abcb83ab3aa'
    ports:
      - '5701:5601'
    networks:
      - elasticsearch-kibana-node-js-logging
```

Kibana container will be running at port number `5701` of host machine. You can browse at `http://localhost:5701/` 

Once everything is setup, youre full `docker-compose.yml` file will look like:

```js
version: '3.7'
services:
  elasticsearch:
    image: 'docker.elastic.co/elasticsearch/elasticsearch:8.10.2'
    container_name: elasticsearch-logging
    volumes:
      - 'data_es:/usr/share/elasticsearch/data'
    environment:
      - discovery.type=single-node
      - CLI_JAVA_OPTS=-Xms2g -Xmx2g
      - bootstrap.memory_lock=true
      - xpack.security.enabled=false
      - xpack.security.enrollment.enabled=false
    ports:
      - '9300:9200'
    networks:
      - elasticsearch-kibana-node-js-logging
  kibana:
    image: 'docker.elastic.co/kibana/kibana:8.10.2'
    container_name: kibana-logging
    environment:
      - 'ELASTICSEARCH_HOSTS=http://elasticsearch:9200'
      - 'XPACK_ENCRYPTEDSAVEDOBJECTS_ENCRYPTIONKEY=d1a66dfd-c4d3-4a0a-8290-2abcb83ab3aa'
    ports:
      - '5701:5601'
    networks:
      - elasticsearch-kibana-node-js-logging
  app:
    build: .
    container_name: app-logging
    restart: unless-stopped
    ports:
      - '3002:8000'
    stdin_open: true
    tty: true
    networks:
      - elasticsearch-kibana-node-js-logging
volumes:
  data_es:
    driver: local
networks:
  elasticsearch-kibana-node-js-logging: null
```

Now we have the configuration for the Node app, Kibana, and Elasticsearch in the `docker-compose.yml` file. The next step is to connect the Node app to Elasticsearch to store the logs so that we can view and monitor from Kibana. For this, we need to install the following Node package to the in the  app and add some logics to send the logs to the elasticsearch.

```js
npm i winston --save
npm i winston-elasticsearch --save
```

[winston]( https://www.npmjs.com/package/winston) is designed to be a simple and universal logging library with support for multiple transports. A transport is essentially a storage device for your logs. Each winston logger can have multiple transports. 

[winston-elasticsearch](https://www.npmjs.com/package/winston-elasticsearch) is an elasticsearch transport for the winston logging toolkit.

After that, here is how you need to create a new logger object. You can crete logger object in the `index.js` file or you can create new file so that you can import it into `index.js` file. I am createing new `logger.js` file with belwo configuration.
```js
const winston = require('winston');
const { ElasticsearchTransport } = require('winston-elasticsearch');

const esTransportOpts = {
  level: 'info',
  index: 'an-index',
  clientOpts: { 
    node: 'http://elasticsearch:9200',
  },
  transformer: logData => {
    return {
      "@timestamp": (new Date()).getTime(),
      severity: logData.level,
      message: `[${logData.level}] LOG Message: ${logData.message}`,
      fields: {}
    }
}
};

const logger = winston.createLogger({
  level: 'info',
  format: winston.format.json(),
  transports: [
    new winston.transports.File({ filename: 'logfile.log', level: 'error' }), // Save errors to file
    new ElasticsearchTransport(esTransportOpts) // Use ElasticsearchTransport instead of Elasticsearch
  ]
});

if (process.env.NODE_ENV !== 'production') {
  logger.add(new winston.transports.Console({ // Log to console if not in production
    format: winston.format.simple()
  }));
}

module.exports = logger;
```

You need to keep few things in mind while creating the logger object.

**index** : is the index name where you will be storing all the logs that are sent form node app.

**node** : You need to define the elasticsearch contine URL.  For this project URL is defined in `docker-compose.yml` file. Make sure you will be defining the internal URL not extranl URL.

You need to import the `logger.js` file in the `index.js` file so that node app can send the logs to the elasticsearch.

```js
var express = require('express');
var app = express();
const logger = require('./logger');// import the logger

app.get('/', function (req, res) {
  res.send('Hello World.');
  //we will be sending these two logs to the elastic search
  logger.info('I am infor message'); 
  logger.error('I am error message');
});

app.listen(8000, function () {
  console.log('Listening on port 8000!');
});
```

Now finally you can run the belwo command to run all the containers. 

```
docker compose up
```

 Above command will take few minutes to build the docker images and run the containers. Once, docker containers are up and running, following containers endpoint should be accessable via browser.
 
1. Node App URL: `http://localhost:3002/`
![Node App](/images/posts/implementing-logging-using-node-js-elasticsearch-kibana-and-docker/app.png#center)
2. Elasticsearch: `http://localhost:9300/`
![Elasticsearch](/images/posts/implementing-logging-using-node-js-elasticsearch-kibana-and-docker/elasticsearch.png#center)
3. Kibana: `http://localhost:5701/`
![Kibana](/images/posts/implementing-logging-using-node-js-elasticsearch-kibana-and-docker/kibana.png#center)


# Visulize the logs in Kibana
You can do many things in the Kibana. But, here we will be only visulizing the logs that are sent form node app.
Open Kibana URL `http://localhost:5701/` and go to discover from left menu. Here you can visulize the logs that are sent form the node app and stored in the elastic search.
![Kibana Logs](/images/posts/implementing-logging-using-node-js-elasticsearch-kibana-and-docker/kibana-logs.png#center)

In above image we can see the log message that are sent form node js app inside the red circle.

**In this post I have send logs directly from node application to elastic search. But, in real world we can ingest logs from a Node.js  application and deliver them securely into an Elasticsearch using [Filebeats](https://www.elastic.co/beats/filebeat).**

The blog post provides a practical guide on integrating Elasticsearch, and Kibana with a Node.js app by using the Winston js library for effective logging and monitoring.

This post covers setting up the development environment, creating a Node.js app, Dockerizing it, and integrating Elasticsearch and Kibana using docker-compose.

The post serves as a concise resource for developers aiming to implement robust logging solutions in their Node.js applications.

You can access the full working example at Github Repository : https://github.com/dev-scripts/implementing-logging-using-nodejs-elasticsearch-kibana-and-docker