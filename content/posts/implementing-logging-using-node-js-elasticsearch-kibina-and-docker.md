---
title: "Implementing Logging Using Node.Js, Elasticsearch, Kibina and Docker"
date: 2024-06-10T16:23:03+10:00
showToc: true
TocOpen: true
tags : [
  "System Design",
  "Software Development",
]
categories : [
  "System Design",
  "Software Development"
]
keywords: [
  "Logging and monitoring",
  "Node.js logging with Elasticsearch and Kibana", 
  "Dockerize Node.js app with Elasticsearch",
  "Kibana log visualization Node.js",
  "Node.js logging tutorial with Docker", 
  "Elasticsearch Kibana integration with Node.js",
  "Real-world logging setup Node.js Elasticsearch", 
  "Monitor Node.js app logs Kibana", 
  "Set up Elasticsearch and Kibana for Node.js", 
  "Docker compose Elasticsearch Kibana Node.js", 
  "Winston logging Node.js Elasticsearch tutorial",
  "Implementing Logging Using Node.Js, Elasticsearch, Kibina and Docker"
]
cover:
    image: "images/posts/implementing-logging-using-node-js-elasticsearch-kibina-and-docker/implementing-logging-using-node-js-elasticsearch-kibina-and-docker.svg" # image path/url
    alt: "Implementing Logging Using Node.Js, Elasticsearch, Kibina and Docker"
   
    relative: false
author: "Prakash Bhandari"
description: "Logging and monitoring play a very important role in software development. They help to identify bugs and understand the application's health."
---

Logging and monitoring play a very important role in software development. It helps to debug and understand the application's health.

Logging and monitoring has numerous benefits, in this blog post, we will focus on building a real-world example to store application logs in Elasticsearch via a Node.js app  by using the Winston js library and visualize them using Kibana. Additionally, we will dockerize the entire application.

# Prerequisite 
Following tools need to be installed in your machine.
1. Docker 
2. Node

# Create a Node.js App and install required packages
Here are the steps to create a node.js app

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

To Dockerize the first step is to create `Dockerfile` with belwo defination.

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

 # Add Elasticsearch image to the `docker-compose.yml` file
 Add below configuration to the `docker-compose.yml` file so that when you run the `docker compose up` command it will pull image form docker hub and buld elasticsearch container.
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

# Kibina image to the `docker-compose.yml` file
Add the following configuration to the `docker-compose.yml` file so that when you run the `docker compose up` command, it will pull the image from Docker Hub and build the Kibana container. Kibana is the tool used to visualize logs and interface to the Elasticsearch.
```js 
kibana:
    image: 'docker.elastic.co/kibana/kibana:8.10.2'
    container_name: kibina-logging
    environment:
      - 'ELASTICSEARCH_HOSTS=http://elasticsearch:9200'
      - 'XPACK_ENCRYPTEDSAVEDOBJECTS_ENCRYPTIONKEY=d1a66dfd-c4d3-4a0a-8290-2abcb83ab3aa'
    ports:
      - '5701:5601'
    networks:
      - elasticsearch-kibana-node-js-logging
```

kibana container will be running at port number `5701` of host machine. You can browse at `http://localhost:5701/` 

Here is the full `docker-compose.yml` file.

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
    container_name: kibina-logging
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

Now we have the configuration for the Node app, Kibana, and Elasticsearch in the docker configuration. The next step is to connect the Node app to Elasticsearch to store the logs so that we can view and monitor the logs in Kibana. For this, we need to install the following Node package to the Node project.

```js
npm i winston --save
npm i winston-elasticsearch --save
```

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

You need to keep few things in mind whicke creating the logger object.

**index** :  is the index name where you will be storing all the logs that are sent form node app.

**node** :  You need to define the elasticsearch contine URL.  For this project URL is defined in `docker-compose.yml` file. Make sure you will be defining the internal URL not extranl URL.

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

Now finally you can run the belwo command 
```
docker compose up
```

 Above command will take few minute to build the docker images and run the containers. Once, docker containers are up and running, following containers endpoint should be accessable via browser.
 
1. Node App URL: `http://localhost:3002/`



![Node App](/images/posts/implementing-logging-using-node-js-elasticsearch-kibina-and-docker/app.png#center)
2. Elasticsearch: `http://localhost:9300/`
![Elasticsearch](/images/posts/implementing-logging-using-node-js-elasticsearch-kibina-and-docker/elasticsearch.png#center)
3. Kibina: `http://localhost:5701/`
![Kibina](/images/posts/implementing-logging-using-node-js-elasticsearch-kibina-and-docker/kibina.png#center)


# Visulize the logs in Kibina
You can do many things in the Kibina. But, here we will be only visulizing the logs that are sent form node app.
Open Kibina URL `http://localhost:5701/` and go to discover from left menu. Here you can visulize the logs that are sent form the node app and stored in the elastic search.
![Kibina Logs](/images/posts/implementing-logging-using-node-js-elasticsearch-kibina-and-docker/kibina-logs.png#center)

In above image we can see the log message that are sent form node js app inside the red circle.

The blog post provides a practical guide on integrating Elasticsearch, and Kibana with a Node.js app by using the Winston js library for effective logging and monitoring.

This post covers setting up the development environment, creating a Node.js app, Dockerizing it, and integrating Elasticsearch and Kibana using docker-compose.

The post serves as a concise resource for developers aiming to implement robust logging solutions in their Node.js applications.