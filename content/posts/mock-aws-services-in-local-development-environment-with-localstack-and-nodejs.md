---
title: "Mock AWS Services in Local Development Environment with LocalStack and NodeJS"
date: 2022-11-19T15:09:02+11:00
# weight: 1
# aliases: ["/first"]
tags: ["AWS", "Localstack", "NodeJS",]
categories : [ "Cloud", ]
author: "Prakash Bhandari"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
UseHugoToc: true
cover:
    image: "images/posts/mock-aws-services-in-local-development-environment-with-localstack-and-nodejs/localstack-nodejs.png" # image path/url
    alt: "Mock AWS Services in Local Development Environment with LocalStack and NodeJS" 
    caption: "Mock AWS Services in Local Development Environment with LocalStack and NodeJS"
    relative: true
---

I was developing and deploying the applications to dedicated servers, on-premises and shared servers. In 2017, I got an opportunity to work with AWS
for one of the retail online ecommerce web application. Since then primarily, I have been developing AWS based applications.

In my career, I have used many AWS services. Primarily, Route 53, EC2 instances, RDS, SQS, SNS, S3 buckets and so on.

Developing and testing applications in the real AWS account is sometimes time-consuming, costly and has dependency  on the internet.

I was looking for an AWS cloud service emulator as an alternative solution to get rid of all these issues. 
I found LocalStack as a solution.

>In this post I will focus on AWS S3 bucket. I will practically show you how to add LocalStack docker container for NodeJS project. Also, I will demonstrate how to create S3 bucket, list the created bucket names and upload file object to the created S3 buckets.

# What is LocalStack ?

According to LocalStack GitHub page 
>LocalStack  is a cloud service emulator that runs in a single container on your local machine or in your CI environment. With LocalStack, you can run your AWS services like S3 bucket, Lambda functions, SQS, etc  entirely on your local machine without connecting to a remote cloud provider.  Whether you are testing complex CDK applications or Terraform configurations, or just beginning to learn about AWS services, LocalStack helps speed up and simplify your testing and development workflow.

>Basic version of LocalStack supports a growing number of AWS services, like AWS Lambda, S3, Dynamodb, Kinesis, SQS, SNS, and many more.

>The Pro version of LocalStack supports additional APIs and advanced features. You can find a comprehensive list of supported APIs on Feature Coverage page.

# How to configure LocalStack in Local machine?

You can configure LocalStack in two ways. One way is to directly install in your machine. Another way is to run LocalStack inside a container. I always prefer to run inside a docker container.

## Add LocalStack in your NodeJS project

### Create `package.json` file and install the dependencies
```json
{
  "name": "aws_node_app",
  "version": "1.0.0",
  "description": "AWS App",
  "author": "Prakash Bhandari <thebhandariprakash@gmail.com>",
  "main": "server.js",
  "scripts": {
    "start": "node server.js"
  },
  "dependencies": {
    "aws-sdk": "^2.1258.0",
    "crypto": "^1.0.1",
    "express": "^4.16.1",
    "fs": "0.0.1-security"
  }
}
```

### Create `Dockerfile` for the NodeJS project(app)

```Dockerfile
FROM node:19

# Create app directory
WORKDIR /usr/src/app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 8081
CMD [ "node", "server.js" ]
```
### Complete `docker-compose.yaml` file.

`docker-compose.yaml`  will create two containers `aws-app` and `aws-app-localstack`. 
Node app is running inside `aws-app` container and AWS services are running inside the `aws-app-localstack` container.
 1. Node App runs at port number **8081**: `http://localhost:8081/`
 2. LocalStacks runs at port number **4566** : `http://localhost:4566/`


```yaml
version: '3'
services:
  app:
    container_name: 'aws-app'
    build:
      dockerfile: Dockerfile
    extra_hosts:
      - 'host.docker.internal:host-gateway'
    ports:
      - '8081:8081'
    volumes:
      - '.:/usr/src/app'
    networks:
      - aws_app_localstack_network
    depends_on:
      - localstack
  localstack:
    container_name: 'aws-app-localstack'
    image: localstack/localstack
    ports:
      - '127.0.0.1:4566:4566'             # LocalStack Gateway
      - '127.0.0.1:3510-3559:4510-4559'   # external services port range
    environment:
      - DEBUG=${DEBUG-}
      - PERSISTENCE=${PERSISTENCE-}
      - LAMBDA_EXECUTOR=${LAMBDA_EXECUTOR-}
      - LOCALSTACK_API_KEY=${LOCALSTACK_API_KEY-}  # only required for Pro
      - DOCKER_HOST=unix:///var/run/docker.sock
      - SERVICES=s3 # comma-separated list of services
    volumes:
      - 'volume-localstack:/var/lib/localstack'
      - '/var/run/docker.sock:/var/run/docker.sock'
    networks:
      - aws_app_localstack_network
networks:
  aws_app_localstack_network:
    driver: bridge
volumes:
  volume-localstack:
    driver: local
```

### Create the `server.js` and `config.json` files
create `server.js` file in the root of the project and import following packages.
1. `express` : To write the APIs. We will not use much for this project
2. `aws-sdk` : Official AWS SDK package. We will use it to create S3 bucket, list the created bucket names and upload file object to the created S3 buckets.
3. `crypto` : To generate random UUIDs. 
4. and load AWS configuration form `config.json` file

Create `config.json` file and place AWS configuration. For now we will add LocalStack configuration.
```json
{ 
    "accessKeyId":"localstack", 
    "secretAccessKey":"localstack",
    "region": "us-west-2"
}
```
Create `server.js` file and import necessary packages and define the necessary constants for basic NodeJS app.

Also, we are load `credentials` from a JSON file on disk (`config.json`).

There are several ways in `Node.js` to supply your credentials to the SDK. 
Some of these are more secure and others afford greater convenience while developing an application.
You can find more on AWS official documentation. https://docs.aws.amazon.com/sdk-for-javascript/v2/developer-guide/setting-credentials-node.html

```javascript
"use strict";
const express = require("express");
const AWS = require("aws-sdk");
const crypto = require("crypto");
AWS.config.loadFromPath("./config.json"); // loads AWS configuration

// Constants
const PORT = 8081;
const HOST = "0.0.0.0";

/**
 * App
 */
const app = express();
app.listen(PORT, HOST, () => {
  console.log(`Running on http://${HOST}:${PORT}`);
});

```

Add `SLEEP_TIME` and `BUCKET_NAME` in the `server.js` file. `crypto.randomUUID()` is embeded at the end of
`localstack-test-bucket` test s3 bucket to generate random/unique bucket everytime.
```javascript
const BUCKET_NAME = "localstack-test-bucket-" + crypto.randomUUID();
const SLEEP_TIME = 3000;
```

Add sleep function in the `server.js` file.

```javascript
/**
 *  Utility function to for async & wait
 */
function sleep(ms) {
  return new Promise((resolve) => {
    setTimeout(resolve, ms);
  });
}
```

Create S3 service object in the `server.js` file. 
```javascript
/**
 *   Create S3 service object
 */
const S3 = new AWS.S3({
  endpoint: "http://localstack:4566",
  s3ForcePathStyle: true,
});
```

Write a function to create a bucket programmatically. in the `server.js` file
```javascript
/**
 *  Create bucket
 */
function create(bucketName, s3) {
  s3.createBucket({ Bucket: bucketName }, function (err, data) {
    if (err) {
      console.log("Error", err);
    } else {
      console.log("Bucket Location", data.Location);
    }
  });
}
```

Write a function to list S3 bucket programmatically in the `server.js` file
```javascript
/**
 *  List the bucket names
 */
function lists(s3) {
    s3.listBuckets(function (err, data) {
        if (err) {
            console.log("Error", err);
        } else {
            console.log("Buckets Lists", data.Buckets);
        }
    });
}
```

Write a function to upload file object to the S3 bucket programmatically in the `server.js` file. We have `localstack.png` file inside `static` folder. We will upload this file to the localstack
```javascript
/**
 *  Upload file object
 */
function uploadObject(bucketName, s3, filePath, keyName) {
    var fs = require("fs");
    // Read the file
    const file = fs.readFileSync(filePath);

    // Setting up S3 upload parameters
    const uploadParams = {
        Bucket: bucketName,
        Key: keyName, // custome file name that your want save as.
        Body: file,
    };

    s3.upload(uploadParams, function (err, data) {
        if (err) {
            console.log("Error", err);
        }
        if (data) {
            console.log("Upload Success", data.Location);
        }
    });
}
```

Creat main function  where we will be calling all the functions that we created to
create S3 bucket, list bucket and upload files from bucket.

```javascript
/**
 * Main function
 */
async function main() {
  console.log("----------------------STARTED--------------------------- \n\n");

  await sleep(SLEEP_TIME);

  console.log("Creating Bucket...");
  create(BUCKET_NAME, S3);
  await sleep(SLEEP_TIME);

  console.log("Listing Buckets...");
  lists(S3);
  await sleep(SLEEP_TIME);

  console.log("Uploading File object to bucket...");
  uploadObject(
    BUCKET_NAME,
    S3,
    "./static/localstack.png",
    "localstack-feature-image.png"
  );
  await sleep(SLEEP_TIME);

  console.log("----------------------END--------------------------- \n\n");
}
```

Call main function in the `server.js` file. So, that it will perform all the S3 operations.
```javascript
/**
 * calling main function
 */
main();
```

### Complete `server.js` file

`server.js` includes the functions to create bucket, list the created buckets and upload file to the bucket.

```javascript
"use strict";

const express = require("express");
const AWS = require("aws-sdk");
const crypto = require("crypto");
AWS.config.loadFromPath("./config.json");

// Constants
const PORT = 8081;
const HOST = "0.0.0.0";
const BUCKET_NAME = "localstack-test-bucket-" + crypto.randomUUID();
const SLEEP_TIME = 3000;

/**
 *   Create S3 service object
 */
const S3 = new AWS.S3({
  endpoint: "http://localstack:4566",
  s3ForcePathStyle: true,
});

/**
 *  Utility function to for async & wait
 */
function sleep(ms) {
  return new Promise((resolve) => {
    setTimeout(resolve, ms);
  });
}

/**
 *  Create bucket
 */
function create(bucketName, s3) {
  s3.createBucket({ Bucket: bucketName }, function (err, data) {
    if (err) {
      console.log("Error", err);
    } else {
      console.log("Bucket Location", data.Location);
    }
  });
}

/**
 *  List the bucket names
 */
function lists(s3) {
  s3.listBuckets(function (err, data) {
    if (err) {
      console.log("Error", err);
    } else {
      console.log("Buckets Lists", data.Buckets);
    }
  });
}

/**
 *  Upload file object
 */
function uploadObject(bucketName, s3, filePath, keyName) {
  var fs = require("fs");
  // Read the file
  const file = fs.readFileSync(filePath);

  // Setting up S3 upload parameters
  const uploadParams = {
    Bucket: bucketName,
    Key: keyName, // custome file name that your want save as.
    Body: file,
  };

  s3.upload(uploadParams, function (err, data) {
    if (err) {
      console.log("Error", err);
    }
    if (data) {
      console.log("Upload Success", data.Location);
    }
  });
}

/**
 * Main function
 */
async function main() {
  console.log("----------------------STARTED--------------------------- \n\n");

  await sleep(SLEEP_TIME);

  console.log("Creating Bucket...");
  create(BUCKET_NAME, S3);
  await sleep(SLEEP_TIME);

  console.log("Listing Buckets...");
  lists(S3);
  await sleep(SLEEP_TIME);

  console.log("Uploading File object to bucket...");
  uploadObject(
    BUCKET_NAME,
    S3,
    "./static/localstack.png",
    "localstack-feature-image.png"
  );
  await sleep(SLEEP_TIME);

  console.log("----------------------END--------------------------- \n\n");
}

/**
 * calling main function
 */
main();

/**
 * App
 */
const app = express();
app.listen(PORT, HOST, () => {
  console.log(`Running on http://${HOST}:${PORT}`);
});

```

### Run project and get Output
You can run project simply running the docker compose command
`docker compose up`

![Image](/images/posts/mock-aws-services-in-local-development-environment-with-localstack-and-nodejs/result.png#center)

**That's it !!!**


We have successfully created NodeJS app with `LocalStack` emulator for AWS services. In this post, 
we have learned how to create S3 buckets
list created buckets, and upload file objects to the buckets.

For production environment, we just need to change configuration in `config.js` file and replace `endpoint` with production `endpoint`

Get full source code form GitHub: https://github.com/dev-scripts/localstack-nodejs

Output demonstration on YouTube Video : 

{{< youtube r9RMT3bLEQ8 >}}

Twitter Post : 
{{< tweet user="ScriptPrakash" id="1594197783887872001" >}}

# References
1. https://github.com/localstack/localstack
2. https://docs.localstack.cloud/aws/feature-coverage/ 
3. https://docs.aws.amazon.com/sdk-for-javascript/v2/developer-guide/getting-started-nodejs.html






